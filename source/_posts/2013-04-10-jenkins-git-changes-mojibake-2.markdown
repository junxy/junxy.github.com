---
layout: post
title: "Jenkins中Git仓库变更集中文注释显示乱码问题（二）"
date: 2013-04-10 20:04
comments: true
tag: Jenkins Git Maven
categories: tech
---
之前 [一篇文章](http://pcme.info/blog/2013/01/11/jenkins-git-changes-mojibake/) 写过：Jenkins 中使用 Git 仓库中有 Log 日志非 [ASCII](http://en.wikipedia.org/wiki/ASCII) ，变更集会出现乱码以及一些解决过程。如今终于有人提交了这个 BUG 的修复，在 Jenkins 插件 Git Plugin 介绍页面可以看到最新的 [Changelog](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin#GitPlugin-Changelog)，但是目前最新的版本还是 1.3，要等到下一版本发布才能享用？既然都有人提交了修复此 BUG 代码，那自己动手编译一个插件吧，于是尝试在自己的 MBP·13' 把 [git-plugin 源码](https://github.com/jenkinsci/git-plugin) Clone 下来，开始编译插件（Jenkins 插件通过 [Maven](http://en.wikipedia.org/wiki/Apache_Maven) 来编译，最后编译成 .hpi 格式的文件，可以在 Jenkins 直接上传安装），编译过程颇为麻烦，主要是首次编译需要下载很多依赖，而国内网络你懂得，为此还发了一条[推](https://twitter.com/jun_cn/status/321606102598946817)，最后确认2个原因导致编译失败：

1. Clone 下来的源码 Master 分支总是构建失败，可能是代码有问题，具体没去排查，JAVA虽学过，这么多年没用就不折腾了。
2. 切换工作区至 tag：git-1.3.0，发现编译还是有错误，原因硬盘的名称中有空格，路径转换时对空格处理的不一样，导致找不到某些文件，在磁盘工具中把名称改了，发现 Terminal 中名称没改变，还重启了电脑（编译一次要4-5分支，重启只要20秒），重试，编译OK，成功后可以在 target 目录里看到名称为 git.hpi 文件。

既然这样，那就好办了，我可以 Checkout git-1.3.0 稳定版，在稳定版的基础上通过 `git cherry-pick cdf149a73a51861bff07897aa42fa0535b88f99e` 合并 Fixd 的提交，并做了一点点[修改](https://github.com/junxy/git-plugin/compare/git-1.3.1-fix-log-utf8)，最后顺利编译成功，不想折腾可以下载我已经编译好的 [git.hpi](http://vdisk.weibo.com/s/x5uCM)，这样就可以提前享用，不用等官方发布啦。

**相关知识**：

Maven：

`$ mvn package` ＃本地打包

`$ mvn cleanup` ＃清理编译

*以上命令直接在项目根目录，pom.xml 文件所在目录下执行即可*

更多参考：<http://maven.apache.org/guides/getting-started/maven-in-five-minutes.html>

Git:
[git cherry-pike](https://www.kernel.org/pub/software/scm/git/docs/git-cherry-pick.html)
