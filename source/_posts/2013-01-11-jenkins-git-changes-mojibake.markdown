---
layout: post
title: '终于解决Jenkins中Git仓库变更集中文注释显示乱码问题'
date: 2013-1-11 3:17
micolog_id: 30001
comments: true
categories: Tech Git Jenkins DevOps
---
问题描叙： 借用这个图片，也是这位仁兄提到的问题（http://bbs.51cto.com/thread-952489-1.html）
![Jenkins使用Git仓库变更集乱码图示](#//img2.51cto.com/attachments/month_1209/20120903_7e526eb2c0772fad70c68EbGhmDiVZme.png.thumb.jpg)

解决方法：

x:\Jenkins\jenkins.xml 新增<del>蓝色</del>粗体标记参数(-Dfile.encoding=utf-8)，然后重启Jenkins服务，完毕！


&lt;arguments>-Xrs -Xmx256m **-Dfile.encoding=utf-8** -Dhudson.lifecycle=hudson.lifecycle.WindowsServiceLifecycle -jar "%BASE%\jenkins.war" --httpPort=8080&lt;/arguments>


原因： 为什么Jenkins 使用 SVN 仓库不会出现非 ANSI 字符乱码，因为 Git 插件获取变更集时 保存的不是 XML格式文档，虽然后缀都 .xml ，这就导致了显示的时候不知道已什么编码方式来显示，就使用了系统默认编码，中文的也就是 GBK。 而 GIT Commit 注释默认是 UTF-8。

**更新**：发现 msbuild 输出的 log 显示时乱码，想到把 cmd 页码也该为 UTF-8，最后试验了，问题解决。中文系统默认代码页 936 ，需要改为 65001，通过修改注册表的方式：参考：[http://hi.baidu.com/study_together/item/b6dda48330b99be1e496e0f9](http://hi.baidu.com/study_together/item/b6dda48330b99be1e496e0f9)

**2013/04/10更新**：已经有更好的方式来解决:[（二）](http://pcme.info/blog/2013/04/10/jenkins-git-changes-mojibake-2/)
