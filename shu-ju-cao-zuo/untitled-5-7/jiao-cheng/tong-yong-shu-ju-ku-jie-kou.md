# 通用数据库接口

* [**1.** 通用数据库接口](tong-yong-shu-ju-ku-jie-kou.md#通用数据库接口)
  * [**1.1.** 连接池](tong-yong-shu-ju-ku-jie-kou.md#连接池)

## 1. 通用数据库接口 <a id="&#x901A;&#x7528;&#x6570;&#x636E;&#x5E93;&#x63A5;&#x53E3;"></a>

GORM 提供了从当前的 `*gorm.DB` 连接中返回通用的数据库接口的方法 `DB` [sql.DB](http://golang.org/pkg/database/sql/#DB) 。

```text
// 获取通用数据库对象 sql.DB 来使用他的 db.DB() 方法

// Ping
db.DB().Ping()
```

注意： 如果底层的数据库连接不是 `*sql.DB`。就像在事务中，它将返回 nil。

### 1.1. 连接池 <a id="&#x8FDE;&#x63A5;&#x6C60;"></a>

```text
// SetMaxIdleConns 设置空闲连接池中的最大连接数。
db.DB().SetMaxIdleConns(10)

// SetMaxOpenConns 设置数据库连接最大打开数。
db.DB().SetMaxOpenConns(100)

// SetConnMaxLifetime 设置可重用连接的最长时间
db.DB().SetConnMaxLifetime(time.Hour)
```

