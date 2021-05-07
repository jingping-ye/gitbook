# 消息-地鼠文档

> 当普通微信用户向公众账号发消息时，微信服务器将POST消息的XML数据包到开发者填写的URL上。

在快速入门一节中就已经演示了如果收到消息以及对消息进行回复  
在SDK中通过`SetMessageHandler`方法对消息进行接收以及处理

```text
server.SetMessageHandler(func(msg message.MixMessage) *message.Reply {
    
})
```

其中`MixMessage`中包含了一个`MsgType`字段，主要会有以下几种类型：

## 接收普通消息 <a id="bw0iye"></a>

```text
const (
    
    MsgTypeText MsgType = "text"
    
    MsgTypeImage = "image"
    
    MsgTypeVoice = "voice"
    
    MsgTypeVideo = "video"
    
    MsgTypeShortVideo = "shortvideo"
    
    MsgTypeLocation = "location"
    
    MsgTypeLink = "link"
    
    MsgTypeMusic = "music"
    
    MsgTypeNews = "news"
    
    MsgTypeTransfer = "transfer_customer_service"
    
    MsgTypeEvent = "event"
)
```

## 接收事件推送 <a id="e9m94"></a>

如果`msg.MsgType`值为`MsgTypeEvent`则表示接收到的是一个事件推送，通过`msg.EventType`可以判断事件类型，可以为以下几种：

```text
const (
    
    EventSubscribe EventType = "subscribe"
    
    EventUnsubscribe = "unsubscribe"
    
    EventScan = "SCAN"
    
    EventLocation = "LOCATION"
    
    EventClick = "CLICK"
    
    EventView = "VIEW"
    
    EventScancodePush = "scancode_push"
    
    EventScancodeWaitmsg = "scancode_waitmsg"
    
    EventPicSysphoto = "pic_sysphoto"
    
    EventPicPhotoOrAlbum = "pic_photo_or_album"
    
    EventPicWeixin = "pic_weixin"
    
    EventLocationSelect = "location_select"
    
    EventTemplateSendJobFinish = "TEMPLATESENDJOBFINISH"
    
    EventWxaMediaCheck = "wxa_media_check"
)
```

## 被动回复用户消息 <a id="cwsqkc"></a>

当收到用户向公众号发送的消息后可以对发过来的进行一次回复：

现支持一下几种回复：`文本`、`图片`、`图文`、`语音`、`视频`、`音乐`

### 回复文本 <a id="5xatud"></a>

```text
server.SetMessageHandler(func(msg message.MixMessage) *message.Reply {
    
        
        text := message.NewText(msg.Content)
        return &message.Reply{MsgType: message.MsgTypeText, MsgData: text}
})
```

### 回复图片 <a id="a2ljmd"></a>

```text
image := message.NewImage(mediaID)
return &message.Reply{MsgType: message.MsgTypeImage, MsgData: image}
```

其中 `mediaID` 为媒体资源 ID ,可以通过素材管理\(`Material`\)中的接口进行上传

### 回复图文 <a id="7513kd"></a>

```text
article1 := message.NewArticle("测试图文1", "图文描述", "http://图片链接地址", "http://图文链接地址")
articles := []*message.Article{article1}
news := message.NewNews(articles)
return &message.Reply{MsgType: message.MsgTypeNews, MsgData: news}
```

### 回复语音 <a id="48vr3o"></a>

```text
voice := message.NewVoice(mediaID)
return &message.Reply{MsgType: message.MsgTypeVoice, MsgData: voice}
```

其中 `mediaID` 为媒体资源 ID ,可以通过素材管理\(`Material`\)中的接口进行上传

### 回复视频 <a id="1cguku"></a>

```text
video := message.NewVideo(mediaID, "标题", "描述")
return &message.Reply{MsgType: message.MsgTypeVideo, MsgData: video}
```

其中 `mediaID` 为媒体资源 ID ,可以通过素材管理\(`Material`\)中的接口进行上传

### 回复音乐 <a id="dzpx7l"></a>

```text
music := message.NewMusic("标题", "描述", "音乐链接", "高质量音乐链接", "缩略图的媒体id")
return &message.Reply{MsgType: message.MsgTypeMusic, MsgData: music}
```

`NewMusic`参数说明：

| 参数 | 是否必须 | 描述 |
| :--- | :--- | :--- |
| Title | 否 | 音乐标题 |
| Description | 否 | 音乐描述 |
| MusicURL | 否 | 音乐链接 |
| HQMusicUrl | 否 | 高质量音乐链接，WIFI环境优先使用该链接播放音乐 |
| ThumbMediaId | 否 | 缩略图的媒体id，通过素材管理中的接口上传多媒体文件，得到的id |

其中不必须的参数可以填写空字符串`""`

### 多客服消息转发 <a id="e73cb2"></a>

请参考 多客服消息转发

文档更新时间: 2020-08-19 10:29   作者：kuteng

