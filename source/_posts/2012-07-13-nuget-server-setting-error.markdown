---
layout: post
title: 'NuGet Server 配置 错误：未能从程序集“Nuget.Server, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null”中加载类型“NuGet.Server.DataServices.Packages”'
date: 2012-7-13 6:15
micolog_id: 14002
comments: false
---
上次配置 nuget server 时遇到些错误：未能从程序集&amp;ldquo;Nuget.Server, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null&amp;rdquo;中加载类型&amp;ldquo;NuGet.Server.DataServices.Packages&amp;rdquo;。，还以为是因为服务器的 webdav 有关（当时没报出错误，只是访问会出现404之类的错误），今天赫然发现，我建项目时的命名和nuget server引用的dll有些相同，于是乎，重命名建了个项目，发布到服务器上，貌似可以了，总结nuget server配置可能的几个原因：

&lt;ol&gt;
&lt;li&gt;webdav冲突，在iis &amp;ldquo;处理程序映射&amp;rdquo;中删除/禁用 webdav&lt;/li&gt;
&lt;li&gt;新建项目名称和nuget server名称冲突，换一个名称&lt;/li&gt;
&lt;li&gt;nuget server 已知bug，更新至最新版本&lt;/li&gt;
&lt;/ol&gt;
标题所示说的问题应该不是问题，当时我SB了。

Okay, enjoy it!
