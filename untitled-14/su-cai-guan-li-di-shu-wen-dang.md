# 素材管理-地鼠文档

## 获取素材操作实例 <a id="6ze5ym"></a>

```text
oa := wc.GetOfficialAccount(cfg)
m:=oa.GetMaterial()
```

## 新增永久图文素材 <a id="56ky90"></a>

```text
AddNews(articles []*Article)
```

## 新增其他类型永久素材\(除视频\) <a id="fpcu7x"></a>

```text
AddMaterial(mediaType MediaType, filename string)
```

## 新增永久视频素材 <a id="50n9qx"></a>

```text
AddVideo(filename, title, introduction string)
```

## 删除永久素材 <a id="254yud"></a>

```text
DeleteMaterial(mediaID string)
```

## 批量获取永久素材 <a id="2lp8hh"></a>

```text
BatchGetMaterial(permanentMaterialType PermanentMaterialType, offset, count int64) 
```

## 获取永久图文素材 <a id="bsrlwb"></a>

```text
GetNews(id string) ([]*Article, error) 
```

文档更新时间: 2020-08-19 10:36   作者：kuteng

