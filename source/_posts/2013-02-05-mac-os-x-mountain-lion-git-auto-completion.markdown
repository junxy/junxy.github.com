---
layout: post
title: "Mac OS X Mountain Lion GIT Auto Completion"
date: 2013-02-05 13:31
comments: true
categories: Tech Git Mac
---
MacBook Pro 很早就安装了 Git，平时用的不多，也就没配置自动完成提示，今天有点时间在 Mac 上，想把自动提示配置加进去，网上找了发现有不同的方法，这里就记录下符合我这种情况下的。首先，要安装 Git，怎么安装就不说了，Google 上有很多教程，我是通过 Homebrew 安装的，
运行： $ `brew install git bash-completion`
安装过了的 brew 会提示： 

``` bash
	Error: git-1.8.1.2 already installed
	Error: bash-completion-1.3 already installed
```

接着， besh $ `vim ~/.bash_profile`

加入下面代码：
``` bash
	if [ -f `brew --prefix`/etc/bash_completion ]; then
    	. `brew --prefix`/etc/bash_completion
	fi
```
	
保存，重新进入 bash，$ `git c` 按两下 `Tab` 键，看到下面提示，配置就完成了。 

``` bash
	checkout                 ci                       clean                    column                   credential               
	cherry                   citool                   clone                    commit                   credential-osxkeychain   
	cherry-pick              cl                       co                       config                   
```
	
参考：<https://github.com/bobthecow/git-flow-completion/wiki/Install-Bash-git-completion>
