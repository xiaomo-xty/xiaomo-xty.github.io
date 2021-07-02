---
title: hexo博客的个性化和备份
date: 2021-02-19 20:43:55
tags: [目录,note,hexo,blog,yilia,valine]
copyright: true
---

![img](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=3010013829,2497003625&fm=26&gp=0.jpg)

部署完博客环境后，初步学会使用，我就想使这个网页更加美丽、强大！并且为了本地源码不丢失，我立马想到了备份。（之前因为电脑崩盘丢失了好多源码文件）

<!--more-->


由于时间仓促，以下内容均先排版放链接，以后再整理

## 主题设置

想要外貌美，首先是yilia主题的各种设置

[yilia个性化](https://blog.csdn.net/qq_37513473/article/details/88617281)
[最全修改](https://blog.csdn.net/xdfwsl/article/details/102587821?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control&dist_request_id=bb7488ed-3eeb-4968-b576-669574a55ce5&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control)



##  valine评论系统加持

我之前实在没想到一个静态网站居然还能有评论功能！![niubi](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=28477601,1058229750&fm=26&gp=0.jpg)

我立马找到相关教程，进行适配。很遗憾，网易云跟帖凉了，多说凉了，gitment作者没有再维护服务器，我最后选用了畅言，之后觉得它太臃肿，我又找，此时。。。

valine出现在我眼前！好家伙，我直接好家伙！（说不了了，先上链接）

[yilia+valine](https://blog.csdn.net/weixin_41287260/article/details/103049437)



### 添加 RSS 订阅支持（可选的）

[rss订阅](https://blog.csdn.net/mudooo/article/details/94584034)



本主题中还使用到了 [hexo-generator-feed](https://links.jianshu.com/go?to=https%3A%2F%2Fyafine-blog.cn%2Fgo.html%3Furl%3DaHR0cHM6Ly9naXRodWIuY29tL2hleG9qcy9oZXhvLWdlbmVyYXRvci1mZWVk) 的 Hexo 插件来做 `RSS`，安装命令如下：



```bash
npm install hexo-generator-feed --save
```

在 Hexo 根目录下的 `_config.yml` 文件中，新增以下的配置项：



```yaml
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
  content_limit: 140
  content_limit_delim: ' '
  order_by: -date
```



##  github备份

github本来作为一个代码托管仓库，本来是好好的仓库。。。现在，程序猿们发挥他们的创造力，早变成了托管网站，备份网站，~~gay的hub~~。。。此博客正是由github免费托管，上文提到的gitment，若不是服务器没了，我是准备用的，备份。。。还是github

[github备份](https://blog.csdn.net/qq_37391214/article/details/100186909)