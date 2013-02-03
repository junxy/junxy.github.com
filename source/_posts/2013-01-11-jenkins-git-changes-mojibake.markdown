---
layout: post
title: '终于解决Jenkins中Git仓库变更集中文注释显示乱码问题'
date: 2013-1-11 3:17
micolog_id: 30001
comments: false
---
问题描叙： 借用这个图片，也是这位仁兄提到的问题（http://bbs.51cto.com/thread-952489-1.html）

&lt;img src=&quot;http://img2.51cto.com/attachments/month_1209/20120903_7e526eb2c0772fad70c68EbGhmDiVZme.png.thumb.jpg&quot; alt=&quot;Jenkins使用Git仓库变更集乱码图示&quot; width=&quot;600&quot; height=&quot;273&quot; /&gt;

&amp;nbsp;

解决方法：

x:\Jenkins\jenkins.xml&amp;nbsp; 新增蓝色粗体标记参数，然后重启Jenkins服务，完毕！

&amp;lt;arguments&amp;gt;-Xrs -Xmx256m &lt;span style=&quot;color: #0000ff;&quot;&gt;&lt;span style=&quot;background-color: #ffffff;&quot;&gt;&lt;strong&gt;-Dfile.encoding=utf-8&lt;/strong&gt;&lt;/span&gt;&lt;/span&gt; -Dhudson.lifecycle=hudson.lifecycle.WindowsServiceLifecycle -jar &quot;%BASE%\jenkins.war&quot; --httpPort=8080&amp;lt;/arguments&amp;gt;

原因： 为什么Jenkins 使用 SVN 仓库不会出现非 ANSI 字符乱码，因为 Git 插件获取变更集时 保存的不是 XML格式文档，虽然后缀都 .xml ，这就导致了显示的时候不知道已什么编码方式来显示，就使用了系统默认编码，中文的也就是 GBK。 而 GIT Commit 注释默认是 UTF-8。

&lt;strong&gt;更新&lt;/strong&gt;：发现 msbuild 输出的 log 显示时乱码，想到把 cmd 页码也该为 UTF-8，最后试验了，问题解决。中文系统默认代码页&amp;nbsp;936 ，需要改为 65001，通过修改注册表的方式：参考：&lt;a href=&quot;http://hi.baidu.com/study_together/item/b6dda48330b99be1e496e0f9&quot;&gt;http://hi.baidu.com/study_together/item/b6dda48330b99be1e496e0f9&lt;/a&gt;

&amp;nbsp;
