---
title: Hexo NexT 主题优化：显示文章阅读次数
tags:
  - Hexo
categories:
  - Hexo
date: 2018-01-03 15:31:05
---


NexT主题(当前版本___5.1.3___)自身已集成了显示文章阅读次数，可在***themes/next***下的_config.yml文件找到如下代码：

```bash
# Show number of visitors to each article.
# You can visit https://leancloud.cn get AppID and AppKey.
leancloud_visitors:
  enable: false
  app_id: <app_id>
  app_key: <app_key>
```

可以看到，这是基于leancloud,需要先[注册leancloud帐号](https://leancloud.cn)获取app_id和app_key。

## 设置leancloud帐号
- 注册并登录leancloud后，创建一个新应用
  {% img /images/2018-01-03/cl-01.png %}
- 进入创建的应用，选择 设置-应用key，能看到你的App Key 和 AppID
  {% img /images/2018-01-03/cl-02.png %}
- 还要设置一下安全域名，把你的域名加到白名单
  {% img /images/2018-01-03/cl-03.png %}
- 还差最重要的一步，离成功就不远了。__创建一个数据库（Class）并命名为 Counter__ 。没有这一步，前端请求的时候会报接口404。
  {% img /images/2018-01-03/cl-04.png %}

好了，leancloud的设置完成了。

## 修改_config.yml
打开themes/next 下的 __config.yml__，把上文提到的代码部分改为：
```bash
leancloud_visitors:
  enable: true
  app_id: <你的app_id>
  app_key: <你的app_key>
```
是的，就这样，你的博客就可以显示阅读数量了。在leancloud应用的 存储-Counter 可查看记录。

## 效果展示
{% img /images/2018-01-03/cl-05.png %}
{% img /images/2018-01-03/cl-06.png %}
