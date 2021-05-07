# 钩子

## 1. 钩子 <a id="&#x94A9;&#x5B50;"></a>

### 1.1. 对象的生命周期 <a id="&#x5BF9;&#x8C61;&#x7684;&#x751F;&#x547D;&#x5468;&#x671F;"></a>

钩子是一个在 插入/查询/更新/删除 之前或之后被调用的方法。

如果你在一个模型中定义了特殊的方法，它将会在插入，更新，查询，删除的时候被自动调用，如果任何的回调抛出错误，GORM 将会停止将要执行的操作并且回滚当前的改变。

### 1.2. 钩子 <a id="&#x94A9;&#x5B50;_1"></a>

#### 1.2.1. 创建一个对象 <a id="&#x521B;&#x5EFA;&#x4E00;&#x4E2A;&#x5BF9;&#x8C61;"></a>

可用于创建的钩子

```text
// 开启事务
BeforeSave
BeforeCreate
// 连表前的保存
// 更新时间戳 `CreatedAt`, `UpdatedAt`
// 保存自己
// 重载哪些有默认值和空的字段
// 链表后的保存
AfterCreate
AfterSave
// 提交或回滚事务
```

代码例子:

```text
func (u *User) BeforeSave() (err error) {
    if u.IsValid() {
        err = errors.New("can't save invalid data")
    }
    return
}

func (u *User) AfterCreate(scope *gorm.Scope) (err error) {
    if u.ID == 1 {
    scope.DB().Model(u).Update("role", "admin")
  }
    return
}
```

注意，在 GORM 中的保存/删除 操作会默认进行事务处理，所以在事物中，所有的改变都是无效的，直到它被提交为止:

```text
func (u *User) AfterCreate(tx *gorm.DB) (err error) {
    tx.Model(u).Update("role", "admin")
    return
}
```

#### 1.2.2. 更新一个对象 <a id="&#x66F4;&#x65B0;&#x4E00;&#x4E2A;&#x5BF9;&#x8C61;"></a>

可用于更新的钩子

```text
// 开启事务
BeforeSave
BeforeUpdate
// 链表前的保存
// 更新时间戳 `UpdatedAt`
// 保存自身
// 链表后的保存
AfterUpdate
AfterSave
// 提交或回滚的事务
```

代码示例:

```text
func (u *User) BeforeUpdate() (err error) {
    if u.readonly() {
        err = errors.New("read only user")
    }
    return
}

// 在事务结束后，进行更新数据
func (u *User) AfterUpdate(tx *gorm.DB) (err error) {
  if u.Confirmed {
    tx.Model(&Address{}).Where("user_id = ?", u.ID).Update("verfied", true)
  }
    return
}
```

#### 1.2.3. 删除一个对象 <a id="&#x5220;&#x9664;&#x4E00;&#x4E2A;&#x5BF9;&#x8C61;"></a>

可用于删除的钩子

```text
// 开启事务
BeforeDelete
// 删除自身
AfterDelete
// 提交或回滚事务
```

代码示例:

```text
// 在事务结束后进行更新数据
func (u *User) AfterDelete(tx *gorm.DB) (err error) {
  if u.Confirmed {
    tx.Model(&Address{}).Where("user_id = ?", u.ID).Update("invalid", false)
  }
    return
}
```

#### 1.2.4. 查询一个对象 <a id="&#x67E5;&#x8BE2;&#x4E00;&#x4E2A;&#x5BF9;&#x8C61;"></a>

可用于查询的钩子

```text
// 从数据库中读取数据
// 加载之前 (急于加载)
AfterFind
```

代码示例:

```text
func (u *User) AfterFind() (err error) {
  if u.MemberShip == "" {
    u.MemberShip = "user"
  }
    return
}
```

