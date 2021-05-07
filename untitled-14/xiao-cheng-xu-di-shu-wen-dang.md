# 小程序-地鼠文档

```text
wc := wechat.NewWechat()
memory := cache.NewMemory()
cfg := &miniConfig.Config{
    AppID:     "xxx",
    AppSecret: "xxx",
    Cache: memory,
}
miniprogram := wc.GetMiniProgram(cfg)
//TODO 调用对应接口
miniprogram.GetAnalysis().GetAnalysisDailyRetain()
```

文档更新时间: 2020-08-19 10:32   作者：kuteng

