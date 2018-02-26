---
title: Hexo同步源文件至GitHub
tags:
  - Hexo
comments: true
date: 2018-02-26 17:00:34
categories: 杂七杂八
---

以前用Hexo最大的痛点就是想在其他电脑上写文章然后更新站点很麻烦，或者换系统后发现忘记备份源文件，然后以前写的东西又得通过各种方法找回再重新部署。没办法，静态站点就是这样。

此时程序员思维就出现了，能不能在我每次写完新文章后自动备份源文件，或者我在`hexo d`的时候一起把我的`.md`文件也deploy上去。

当然，方法各种，这里我直接使用 [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git) 这个小插件的功能，相对简单些。

<!--more-->

# 安装方法

直接一句话搞定：

```shell
npm install hexo-deployer-git --save
```

当然，作者说如果想使用最新版，可用下面的命令：

- 对于npm版本低于4的用户

```shell
$ npm install git+git@github.com:hexojs/hexo-deployer-git.git --save
```

- 对于npm版本低于5的用户

```shell
$ npm install git+ssh://git@github.com:hexojs/hexo-deployer-git.git --save
```

> 查看自己npm版本的命令：`npm --version`



其实如果你原先就是将hexo部署在GitHub Pages，那这个插件是以及安装过了，只需要进行相关配置就行。

# 配置

修改hexo根目录下的`_config.yml`文件：

```shell
# _config.yaml
deploy:
  - type: git
    repo: git@github.com:<username>/<username>.github.io.git
    branch: master
  - type: git
    repo: git@github.com:<username>/<username>.github.io.git
    branch: src
    extend_dirs: /
    ignore_hidden: false
    ignore_pattern:
        public: .
```

## 说明

- 第一个master分支是你静态页面所在分支，也就是`hexo g`后生成的`public`文件夹中的内容
- 第二个src分支就是备份你源文件的，具体参数请参考原作者写的




如此之后，在每次`hexo d`后就能将你的源文件备份至`src`分支，没有更爽。

当然，安装后每次 deploy 会比原来稍慢点。

还有，未配置过`git user`信息的会提示要配置user.mail和user.name：

```shell
  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"
```



# 参考

-  [hexo-deployer-git 的GitHub地址](https://github.com/hexojs/hexo-deployer-git) 
-  [Hexo官方关于deployment的说明](https://hexo.io/zh-cn/docs/deployment.html)
-  [知乎上关于换电脑后hexo该怎么更新的回答](https://www.zhihu.com/question/21193762)



本文完。