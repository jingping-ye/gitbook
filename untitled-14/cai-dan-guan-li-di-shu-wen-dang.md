# 菜单管理-地鼠文档

## 获取菜单操作实例 <a id="gdc0ro"></a>

```text
oa := wc.GetOfficialAccount(cfg)
m:=oa.GetMenu()
```

## 获取当前菜单设置 <a id="7wloir"></a>

```text
GetMenu() (resMenu ResMenu, err error)
```

其中`ResMenu`结果为：

```text
//ResMenu 查询菜单的返回数据
type ResMenu struct {
    util.CommonError

    Menu struct {
        Button []Button `json:"button"`
        MenuID int64    `json:"menuid"`
    } `json:"menu"`
    Conditionalmenu []resConditionalMenu `json:"conditionalmenu"`
}
```

## 添加菜单（struct方式） <a id="4pzgv4"></a>

```text
SetMenu(buttons []*Button) error
```

## 添加菜单（JSON方式） <a id="7tr73i"></a>

直接传入json

```text
SetMenuByJSON(jsonInfo string) error
```

## 删除菜单 <a id="ghzx0g"></a>

```text
DeleteMenu() error
```

## 添加个性化菜单（struct方式） <a id="2x0wyy"></a>

```text
AddConditional(buttons []*Button, matchRule *MatchRule) error
```

## 添加个性化菜单（JSON方式） <a id="fidks9"></a>

直接传入json

```text
AddConditionalByJSON(jsonInfo string) error
```

## 测试个性化菜单匹配结果 <a id="f35id7"></a>

```text
MenuTryMatch(userID string) (buttons []Button, err error)
```

## 删除个性化菜单 <a id="a12pmn"></a>

```text
DeleteConditional(menuID int64) error
```

## 获取自定义菜单配置接口 <a id="3o9qfl"></a>

```text
GetCurrentSelfMenuInfo() (resSelfMenuInfo ResSelfMenuInfo, err error)
```

其中`ResSelfMenuInfo`结果为：

```text
type ResSelfMenuInfo struct {
    util.CommonError

    IsMenuOpen   int32 `json:"is_menu_open"`
    SelfMenuInfo struct {
        Button []SelfMenuButton `json:"button"`
    } `json:"selfmenu_info"`
}
```

文档更新时间: 2020-08-19 10:30   作者：kuteng

