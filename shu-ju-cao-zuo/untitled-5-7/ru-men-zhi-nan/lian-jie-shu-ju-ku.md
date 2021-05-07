# 连接数据库

## 1. 连接数据库 <a id="&#x8FDE;&#x63A5;&#x6570;&#x636E;&#x5E93;"></a>

### 1.1. 连接数据库 <a id="&#x8FDE;&#x63A5;&#x6570;&#x636E;&#x5E93;_1"></a>

为了连接数据库，你首先要导入数据库驱动程序。例如：

```text
import _ "github.com/go-sql-driver/mysql"
```

GORM 已经包含了一些驱动程序，为了方便的去记住它们的导入路径，你可以像下面这样导入 mysql 驱动程序

```text
import _ "github.com/jinzhu/gorm/dialects/mysql"
// import _ "github.com/jinzhu/gorm/dialects/postgres"
// import _ "github.com/jinzhu/gorm/dialects/sqlite"
// import _ "github.com/jinzhu/gorm/dialects/mssql"
```

### 1.2. 支持的数据库 <a id="&#x652F;&#x6301;&#x7684;&#x6570;&#x636E;&#x5E93;"></a>

#### 1.2.1. MySQL <a id="mysql"></a>

注意： 为了正确的处理 `time.Time` ，你需要包含 `parseTime` 作为参数。 \([More supported parameters](https://github.com/go-sql-driver/mysql#parameters)\)

```text
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/mysql"
)

func main() {
  db, err := gorm.Open("mysql", "user:password@/dbname?charset=utf8&parseTime=True&loc=Local")
  defer db.Close()
}
```

#### 1.2.2. PostgreSQL <a id="postgresql"></a>

```text
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/postgres"
)

func main() {
  db, err := gorm.Open("postgres", "host=myhost port=myport user=gorm dbname=gorm password=mypassword")
  defer db.Close()
}
```

#### 1.2.3. Sqlite3 <a id="sqlite3"></a>

```text
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/sqlite"
)

func main() {
  db, err := gorm.Open("sqlite3", "/tmp/gorm.db")
  defer db.Close()
}
```

#### 1.2.4. SQL Server <a id="sql-server"></a>

[Get started with SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/go)，它可以通过 Docker 运行在你的 [Mac](https://sqlchoice.azurewebsites.net/en-us/sql-server/developer-get-started/go/mac/)， [Linux](https://sqlchoice.azurewebsites.net/en-us/sql-server/developer-get-started/go/ubuntu/) 上。

```text
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/mssql"
)

func main() {
  db, err := gorm.Open("mssql", "sqlserver://username:password@localhost:1433?database=dbname")
  defer db.Close()
}
```

### 1.3. 不支持的数据库 <a id="&#x4E0D;&#x652F;&#x6301;&#x7684;&#x6570;&#x636E;&#x5E93;"></a>

GORM 官方支持以上四种数据库， 你可以为不支持的数据库编写支持，参考 [GORM Dialects](https://github.com/jinzhu/gorm.io/blob/master/docs/dialects.html)

