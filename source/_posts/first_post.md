---
title: 这个博客的前世今生
date: 2020-05-13 16:47:00
---

今天是个好日子，我也可以很开心地说一声，哈哈，我又回来了。两年前的某个无聊的周末，照哥漫不经心地掏出Mac敲敲打打，然后跟我说，糖酱，你现在有自己的github博客了。当时部署在一个云服务器上，我也没有当回事。直到前几天我想着，我是不是应该开始更新博客了。美滋滋地从github上把仓库clone到本地，直到发现，诶，怎么连个markdown文件都找不到，我才终于认清了这个残酷的现实，那就是我的博客原始文件随着云服务器的过期永远地离开了我，github上只有发布的博客文件。

这两天正好心情好，也想重新把这个博客搭起来。博客还是用Hexo框架，主题也用之前选好的，算是一种传承，当然也要避免重蹈换电脑文件就丢失的覆辙。这个毕竟是个~伪~技术博客，第一篇就记录一下搭建的过程。

### 安装Hexo及初始化

如果是从零开始搭建，可以参考[这个](https://zhuanlan.zhihu.com/p/26625249)。因为我已经有了github.io仓库，也不愿意从头来一遍，所以就从配置Hexo开始。在此之前，要先装好[Git](https://git-scm.com/download/win)和[Node.js](https://nodejs.org/en/download/)。

首先新建一个文件夹，暂时命名为Hexo，在此文件夹下运行git bash。因为hexo初始化只能在空文件夹中进行，所以没有在克隆的github.io文件夹里进行此操作。

```bash
$ npm install -g hexo-cli   # 安装hexo
$ hexo init   # 初始化
$ npm install   # 安装组件
$ npm install hexo-deployer-git --save   # github部署工具
```

### 更换主题

Hexo初始化的主题是landscape，我还是决定换用之前的next主题，更为简洁。这里选择了2020年最新版本的next主题。

```bash
$ git clone https://github.com/next-theme/hexo-theme-next themes/next
```
然后我们到_config.yml文件里启用next主题。

```
theme: next
```
验证一下是否启用成功。

```bash
$ hexo cl   # 清空hexo缓存
$ hexo g   # 生成页面
$ hexo s   # 启动预览
```

访问 localhost:4000，博客已成功更换主题。

### github部署

为了可以在不同的设备上写博客，这里我们采用增加分支的方法来达成这一目的。如果是从头搭建可以参考这个[知乎问题](https://www.zhihu.com/question/21193762)。同样地，我还是从原来的框架上还是做。原来仅有一个master默认分支用于发布文章。现在我们需要新建一个分支用于推送原始文件。我们在这里新建一个hexo分支，并将其定义为默认分支，用来存储所有的原始文件，我们日常的操作都是在这个分支上完成的。修改_config.yml文件，关联自己的github账号。

```
deploy:
  type: git
  repo: https://github.com/username/username.github.io.git
  branch: master
```

接下来，我们将github.io仓库拷贝到本地，将.git文件夹拷贝到之前的Hexo文件夹中，将Hexo作为github.io的本地文件夹，然后开始部署。此时处于默认分支hexo。

```bash
$ git add .
$ git commit -m "brand new start"
$ git push origin hexo
$ hexo g -d   # 生成网站并部署到master分支
```

这里还有一个小的trick。因为本来的master就是默认分支，我懒得改，就新建并设置了hexo分支用于推送博客，结果就404了。原因是hexo默认是往master分支推送文章的，所以一定要将master分支作为发布分支。

### 更新博客

之后就可以很方便的管理博客啦。

```bash
$ hexo n hello-world   # 新建博客文件
$ hexo g -d   # 生成网站并部署到master分支
$ git add .   # 同步原始文件
$ git commit -m "blog posts"
$ git push origin hexo

```

第一篇博客基本上就是这样啦。重新回来还是很开心的。

Hello, world!

你好，世界！





