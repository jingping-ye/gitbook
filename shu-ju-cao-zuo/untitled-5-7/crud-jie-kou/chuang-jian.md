# 创建

## 1. 创建 <a id="&#x521B;&#x5EFA;"></a>

### 1.1. 创建记录 <a id="&#x521B;&#x5EFA;&#x8BB0;&#x5F55;"></a>

```text
user := User{Name: "Jinzhu", Age: 18, Birthday: time.Now()}

db.NewRecord(user) // => 返回 `true` ，因为主键为空

db.Create(&user)

db.NewRecord(user) // => 在 `user` 之后创建返回 `false`
```

### 1.2. 默认值 <a id="&#x9ED8;&#x8BA4;&#x503C;"></a>

你可以通过标签定义字段的默认值，例如：

```text
type Animal struct {
    ID   int64
    Name string `gorm:"default:'galeone'"`
    Age  int64
}
```

然后 SQL 会排除那些没有值或者有 [零值](https://tour.golang.org/basics/12) 的字段，在记录插入数据库之后，gorm将从数据库中加载这些字段的值。

```text
var animal = Animal{Age: 99, Name: ""}
db.Create(&animal)
// INSERT INTO animals("age") values('99');
// SELECT name from animals WHERE ID=111; // 返回的主键是 111
// animal.Name => 'galeone'
```

注意 所有包含零值的字段，像 `0`，`''`，`false` 或者其他的 [零值](https://tour.golang.org/basics/12) 不会被保存到数据库中，但会使用这个字段的默认值。你应该考虑使用指针类型或者其他的值来避免这种情况:

```text
// Use pointer value
type User struct {
  gorm.Model
  Name string
  Age  *int `gorm:"default:18"`
}

// Use scanner/valuer
type User struct {
  gorm.Model
  Name string
  Age  sql.NullInt64 `gorm:"default:18"`
}
```

### 1.3. 在钩子中设置字段值 <a id="&#x5728;&#x94A9;&#x5B50;&#x4E2D;&#x8BBE;&#x7F6E;&#x5B57;&#x6BB5;&#x503C;"></a>

如果你想在 `BeforeCreate` 函数中更新字段的值，应该使用 `scope.SetColumn`，例如：

```text
func (user *User) BeforeCreate(scope *gorm.Scope) error {
  scope.SetColumn("ID", uuid.New())
  return nil
}
```

### 1.4. 创建额外选项 <a id="&#x521B;&#x5EFA;&#x989D;&#x5916;&#x9009;&#x9879;"></a>

```text
// 为插入 SQL 语句添加额外选项
db.Set("gorm:insert_option", "ON CONFLICT").Create(&product)
// INSERT INTO products (name, code) VALUES ("name", "code") ON CONFLICT;
```

