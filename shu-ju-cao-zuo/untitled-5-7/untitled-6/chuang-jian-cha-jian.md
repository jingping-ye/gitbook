# 创建插件

## 1. 创建插件 <a id="&#x521B;&#x5EFA;&#x63D2;&#x4EF6;"></a>

GORM 本身由 `Callbacks` 提供支持，因此你可以根据需要完全自定义GORM。

### 1.1. 注册新的 callback <a id="&#x6CE8;&#x518C;&#x65B0;&#x7684;-callback"></a>

将 callback 注册进如 callbacks：

```text
func updateCreated(scope *Scope) {
    if scope.HasColumn("Created") {
        scope.SetColumn("Created", NowFunc())
    }
}

db.Callback().Create().Register("update_created_at", updateCreated)
// 注册 Create 进程的回调
```

### 1.2. 删除已有的 callback <a id="&#x5220;&#x9664;&#x5DF2;&#x6709;&#x7684;-callback"></a>

从 callbacks 中删除一个 callback：

```text
db.Callback().Create().Remove("gorm:create")
// delete callback `gorm:create` from Create callbacks
```

### 1.3. 替换 callback <a id="&#x66FF;&#x6362;-callback"></a>

替换拥有相同名字的 callback ：

```text
db.Callback().Create().Replace("gorm:create", newCreateFunction)
// replace callback `gorm:create` with new function `newCreateFunction` for Create process
```

### 1.4. 注册 callback 的顺序 <a id="&#x6CE8;&#x518C;-callback-&#x7684;&#x987A;&#x5E8F;"></a>

在注册 callbacks 时设置顺序：

```text
db.Callback().Create().Before("gorm:create").Register("update_created_at", updateCreated)
db.Callback().Create().After("gorm:create").Register("update_created_at", updateCreated)
db.Callback().Query().After("gorm:query").Register("my_plugin:after_query", afterQuery)
db.Callback().Delete().After("gorm:delete").Register("my_plugin:after_delete", afterDelete)
db.Callback().Update().Before("gorm:update").Register("my_plugin:before_update", beforeUpdate)
db.Callback().Create().Before("gorm:create").After("gorm:before_create").Register("my_plugin:before_create", beforeCreate)
```

### 1.5. 自带的 Callbacks <a id="&#x81EA;&#x5E26;&#x7684;-callbacks"></a>

GORM 在处理 CRUD 操作时自带了一些 Callback，建议你在写插件前先熟悉这些 Callback：

* [Create callbacks](https://github.com/jinzhu/gorm/blob/master/callback_create.go)
* [Update callbacks](https://github.com/jinzhu/gorm/blob/master/callback_update.go)
* [Query callbacks](https://github.com/jinzhu/gorm/blob/master/callback_query.go)
* [Delete callbacks](https://github.com/jinzhu/gorm/blob/master/callback_delete.go)
* Row Query callbacks - 默认没有注册的 Callbacks

你可以用以下的方法来注册你的 Callback：

```text
func updateTableName(scope *gorm.Scope) {
  scope.Search.Table(scope.TableName() + "_draft") // append `_draft` to table name
}

db.Callback().RowQuery().Register("publish:update_table_name", updateTableName)
```

请前往查看所有的 API —— [https://godoc.org/github.com/jinzhu/gorm](https://godoc.org/github.com/jinzhu/gorm) 。

