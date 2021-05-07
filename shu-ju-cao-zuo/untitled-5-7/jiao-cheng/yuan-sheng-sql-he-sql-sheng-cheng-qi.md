# 原生 SQL 和 SQL 生成器

## 1. 原生 SQL 和 SQL 生成器 <a id="&#x539F;&#x751F;-sql-&#x548C;-sql-&#x751F;&#x6210;&#x5668;"></a>

### 1.1. 运行原生 SQL <a id="&#x8FD0;&#x884C;&#x539F;&#x751F;-sql"></a>

执行原生 SQL时不能通过链式调用其他方法

```text
db.Exec("DROP TABLE users;")
db.Exec("UPDATE orders SET shipped_at=? WHERE id IN (?)", time.Now(), []int64{11,22,33})

// Scan
type Result struct {
    Name string
    Age  int
}

var result Result
db.Raw("SELECT name, age FROM users WHERE name = ?", 3).Scan(&result)
```

### 1.2. sql.Row 和 sql.Rows <a id="sqlrow-&#x548C;-sqlrows"></a>

使用 `*sql.Row` 或者 `*sql.Rows` 获得查询结果

```text
row := db.Table("users").Where("name = ?", "jinzhu").Select("name, age").Row() // (*sql.Row)
row.Scan(&name, &age)

rows, err := db.Model(&User{}).Where("name = ?", "jinzhu").Select("name, age, email").Rows() // (*sql.Rows, error)
defer rows.Close()
for rows.Next() {
    ...
    rows.Scan(&name, &age, &email)
    ...
}

// 原生SQL
rows, err := db.Raw("select name, age, email from users where name = ?", "jinzhu").Rows() // (*sql.Rows, error)
defer rows.Close()
for rows.Next() {
    ...
    rows.Scan(&name, &age, &email)
    ...
}
```

### 1.3. 扫描 sql.Rows 数据到模型 <a id="&#x626B;&#x63CF;-sqlrows-&#x6570;&#x636E;&#x5230;&#x6A21;&#x578B;"></a>

```text
rows, err := db.Model(&User{}).Where("name = ?", "jinzhu").Select("name, age, email").Rows() // (*sql.Rows, error)
defer rows.Close()

for rows.Next() {
  var user User
  // ScanRows 扫描一行到 user 模型
  db.ScanRows(rows, &user)

  // do something
}
```

