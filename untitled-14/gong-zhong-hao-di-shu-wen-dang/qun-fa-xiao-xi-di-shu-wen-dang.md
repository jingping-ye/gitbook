# 群发消息-地鼠文档

## 获取群发操作实例 <a id="fscrxa"></a>

```text
oa := wc.GetOfficialAccount(cfg)
bd:=oa.GetBroadcast()
//TODO bd.SendText
```

## 发送对象 <a id="48mz38"></a>

* 发送方法的第一个参数为`broadcast.User`对象，为nil则表示发送给所有人
* `broadcast.User{TagID:1}` : 根据tagID发送
* ``broadcast.User{OpenID:[]string{"openid-1","openid-2"}}``` ：根据openid发送

## 群发消息类型 <a id="bvyak5"></a>

### 发送文本消息 <a id="9axmg8"></a>

```text
bd.SendText(user *User, content string)
```

### 发送图文 <a id="tey5f"></a>

```text
bd.SendNews(user *User, mediaID string,ignoreReprint bool)
```

### 发送图片 <a id="9lradx"></a>

```text
bd.SendImage(user *User, images *Image)
```

### 发送语音 <a id="3n69io"></a>

```text
bd.SendVoice(user *User, mediaID string)
```

### 发送视频 <a id="95nc12"></a>

```text
bd.SendVideo(user *User, mediaID string,title,description string)
```

文档更新时间: 2020-08-19 10:26   作者：kuteng

