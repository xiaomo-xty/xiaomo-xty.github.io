---
title: hexo博客环境搭建---（github托管）
date: 2021-02-19 13:25:04
tags: [目录hexo,note,技术]
---


![hexo+github](https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2010232996,1620516668&fm=26&gp=0.jpg)

作为一名未来的极客，博客站点这种东西那必须得自己动手来干！github page + hexo是非常棒的组合，完全免费，可diy程度高，nice！

![nice](https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=4204231927,2946927351&fm=26&gp=0.jpg)

<!--more-->

## 软件准备

1. [nodejs](https://nodejs.org/en/)
2. 


## 部署
1. 更换npm镜像源:为了保证之后的下载速度，更换一个镜像源  
`npm install -g cnpm --registry=https://registry.npm.taobao.org`

2. 一句可能用得到的：`npm config set strict-ssl false`(调成true重新开启)

3. 部署hexo框架  
    `cnpm intall -g hexo-cli`

4. 建立博客目录  

5. 初始化hexo  

  `hexo init`

  tips：可能出现` SSL certificate problem: unable to get local issuer certificate`,使用` git config --global http.sslverify false `

## 写博客
1. 本地启动  
`hexo s`  
会自动打开4000端口，打开http://localhost:4000  进行访问

2. 新建文章  
`hexo new "name"`  
或缩写  
`hexo n "name"`

## 远程推送
1. 安装远程推送插件  
`cnpm install --save hexo-deployer-git`

2. 配置文件 ==_config.yml==  
在 ==deploy:==下配置： 
  >type: 类型  
  >repo: 仓库地址
  >branch: 分支

3. 推送命令：  
`hexo d`

## 主题更换
1. 选择主题，推荐：  
[yilia](https://github.com/litten/hexo-theme-yilia) 以yilia为例
2. 用`git clone`命令将主题项目克隆到 `themes`  
`git clone 地址 本地路径`
3. 更改配置文件`_config.yml`  
将 `theme: landscape`改为`theme: yilia`
4. 生成：  
`hexo g`

---
参考视频：

<div style="position: relative;width: 100%;height: 0;padding-bottom: 75%;">  
  <iframe src="//player.bilibili.com/player.html?aid=44544186&bvid=BV1Yb411a7ty&cid=158772893&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width: 100% height: 100% left: 0 top: 0> </iframe> 
</div>