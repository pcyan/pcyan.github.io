---
title: Linux 使用 Hexo + Github Pages 搭建静态博客
date: 2016-09-07 00:17:00
tags: 
  - Hexo
  - Linux
categories: Linux没那么难
---

本文中使用的 `Linux` 发行版为 `Ubuntu 16.04`
以下内容假设你是一个对 `github` 有所了解并且有使用过 `git` 的人
如果你还是想继续阅读，请利用好你的[搜索引擎](http://google.com)做相应的准备
<!-- more -->

## About Github Pages

[Github Pages](https://pages.github.com/) 是 `github` 的一种特殊的仓库，可以用来展示你的个人信息或者你的项目，不过当然，你可以用它做其他更好玩的事情。

创建并使用 `Github Pages` 的方法很简单，你可以直接看[官方的教程](https://pages.github.com/)，或者看我的~~废话~~简短教程

1. 创建一个名为 `your_username.github.io` 的仓库（your_username表示你的Github用户名）
2. 部署静态页面例如 `index.html` 到刚才创建的仓库
3. 通过 `http://github.com/your_username/your_username.github.io/index.html` 来访问刚才创建的页面

至此你已经可以成功的使用 `Github Pages` 玩耍了
更加高级的用法是你可以购买一个域名，然后将域名与 `Github Pages` 绑定在一起
这样就可以直接使用你自己的域名来访问了
这里没有讲的原因是我没钱买域名（捂脸），所以如果你感兴趣的话可以移步官方教程。

## About Hexo

那么问题来了，我创建一个博客，总不能自己去维护页面的样式主题吧，这样一点都不软件工程啊。
所以接下来就是我们的正餐 -- `Hexo`

[Hexo](https://hexo.io/zh-cn/)是一个跨平台的，可供用户快速配置博客样式并且管理博客的工具
你可以使用别人分享的[主题](https://hexo.io/themes/)加上上文提及的 `Github Pages`  快速搭建你的个人静态博客。

## How to use Hexo

下面讲一下如何在 `Linux` 配置并使用 `Hexo`

### 安装 NodeJs
[官方教程](https://nodejs.org/en/download/package-manager/)
稳定版v4.x
```
curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
sudo apt-get install -y nodejs
```

最新版本v6.x
```
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
```
可选：安装构建工具
```
sudo apt-get install -y build-essential
```
### 安装 Hexo

```
$ cd workspace/
$ npm install hexo-cli -g
$ hexo init //初始化环境
$ cd Hexo
$ npm install //安装依赖
$ hexo clean
$ hexo g //生成文件
$ hexo s //启动预览
```

输入 `hexo s` 后会显示
```
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
```
在浏览器输入 `http://localhost:4000/` 便可以预览博客了

### 主题配置
初始化完 `Hexo` 使用的是默认的主题，如果不喜欢的话可以上`github` 或者 `Hexo` 搜一下自己喜欢的主题
这里提供我使用的主题，[NexT](https://github.com/iissnan/hexo-theme-next)

下载主题
```
$ cd workspace/Hexo
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

配置`NexT`
修改 `workspace/Hexo/_config.yml` 的 `theme`字段
```
theme: next
```

> 这里注意，`Hexo` 的配置文件，冒号后面一定要有空格

保存配置之后便可以使用 `hexo s` 来预览主题了
关于主题的详细配置，[NexT官方网站](http://theme-next.iissnan.com/)讲的比我详细，我就不吹了

## 将 Hexo 部署到 Github

### 生成 SSH 密钥
```
$ ssh-keygen -t rsa -C "your_email@example.com"
# Creates a new ssh key using the provided email
Generating public/private rsa key pair.
Enter file in which to save the key (/home/username/.ssh/id_rsa):
Enter passphrase (empty for no passphrase): [Type a passphrase]
Enter same passphrase again: [Type passphrase again]
```
如果不为密钥配置密码可以直接 `Enter` 过

### Github添加公钥
复制 `~/.ssh/id_rsa.pub`的内容，进入你的 `Github`
选择`Settings –> Deploy keys –> Add deploy key`，添加公钥

测试配置是否成功
```
$ ssh -T git@github.com
```
如果出现以下内容即表示配置完成并且成功！
```
Hi username! You've successfully authenticated, but GitHub does not
provide shell access.
```

### 安装 Git 扩展
这里注意`Hexo` 需要安装扩展才能使用 `Git` 部署
```
$ npm install hexo-deployer-git --save
```

### Git部署配置
编辑 `workspace/Hexo/_config.yml`,修改 `deploy` 字段下的内容
```
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git
  branch: master
```

> 这里 `repo` 填的是你的 `Github Pages` 的地址，注意是 `SSH` 地址，不是 `HTTPS`

### 部署

```
$ hexo clean
$ hexo g -d //组合命令，生成文件后部署
```
部署完毕再次进入 `Github Pages` 就可以看到效果了
有时候部署完会有点延时，等几秒钟再刷新就可以看到效果了

## Finally

Enjoy it !!
