# 用户管理-地鼠文档

## 获取用户管理操作实例 <a id="8tzi0a"></a>

```text
oa := wc.GetOfficialAccount(cfg)
u:=oa.GetUser()
```

## 获取用户基本信息 <a id="4qmjiw"></a>

```text
GetUserInfo(openID string) (userInfo *Info, err error)
```

## 设置用户备注名 <a id="cgr0d3"></a>

```text
UpdateRemark(openID, remark string) (err error)
```

## 返回用户列表 <a id="9d7z3x"></a>

```text
ListUserOpenIDs(nextOpenid ...string) (*OpenidList, error)
```

其中返回结果为：

```text
/ OpenidList 用户列表
type OpenidList struct {
    Total int `json:"total"`
    Count int `json:"count"`
    Data  struct {
        OpenIDs []string `json:"openid"`
    } `json:"data"`
    NextOpenID string `json:"next_openid"`
}
```

文档更新时间: 2020-08-19 10:27   作者：kuteng

