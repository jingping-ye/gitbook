# 模板消息-地鼠文档

## 获取模板消息实例 <a id="d9wrti"></a>

```text
oa := wc.GetOfficialAccount(cfg)
m:=oa.GetTemplate()
```

## 发送模板消息 <a id="b2s9zb"></a>

```text
Send(msg *TemplateMessage) (msgID int64, err error)
```

其中 `TemplateMessage`结构为：

```text
type TemplateMessage struct {
    ToUser     string                       `json:"touser"`          
    TemplateID string                       `json:"template_id"`     
    URL        string                       `json:"url,omitempty"`   
    Color      string                       `json:"color,omitempty"` 
    Data       map[string]*TemplateDataItem `json:"data"`            

    MiniProgram struct {
        AppID    string `json:"appid"`    
        PagePath string `json:"pagepath"` 
    } `json:"miniprogram"` 
}
```

文档更新时间: 2020-08-19 10:31   作者：kuteng

