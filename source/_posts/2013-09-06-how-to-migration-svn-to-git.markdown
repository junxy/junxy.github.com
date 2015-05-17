---
layout: post
title: "如何将 SVN 迁移至 GIT 并保留所有历史记录"
date: 2013-09-06 23:06
comments: true
categories: Tech Git DevOps
---

## 前言

GIT 已经无处不在，你们还在用 SVN 管理源代码吗？如果你和你的小伙伴们正在考虑，从 SVN 迁移至 GIT，如果你们的 SVN 仓库已经够庞大（1W+ commits）和复杂（后面复杂情况详解），又想在迁移之后保留所有更改记录，这篇文章也许正是你要找的。

## 准备工作

需要安装那些软件&工具？

1. [SubGit](http://subgit.com/index.html)
2. [JRE](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
3. [Subversion](http://subversion.apache.org/packages.html#windows) 

[SubGit](http://subgit.com/index.html) 是个提供从 SVN 安全迁移至 GIT 的商业工具软件，这里主要是用到它将 SVN 提交历史翻译为 GIT 提交这一免费功能。其官网提供二进制包[下载](http://subgit.com/download/index.html)（目前最新版本2.0.0，支持远程 SVN 地址）。
因为 SubGit 跨平台，基于 Java 写的，需要安装 [JRE](http://www.oracle.com/technetwork/java/javase/downloads/index.html)（JAVA 运行环境） 才能够运行。
此外，还将用到 SVN 命令，需要安装 [Subversion](http://subversion.apache.org/packages.html#windows) 并配置至 `%PATH%` 环境变量（这里我是直接使用 VisualSVN Server 安装目录下 bin 自带的 Subversion）。

##一. 简单情况

只需要几个步骤就能完成 SVN 至 GIT 的转换：

```bat
＃ 1. 解压 SubGit-2.0.0.zip 至路径如：x:\SubGit-2.0.0，x: 替换为实际分区。
＃ 2. 执行配置，会生成默认配置，如需密码访问远程 SVN 服务器，
＃    在 SubGit_repository_name\SubGit\password 设置访问密码，
＃ 说明：
＃   SubGit_repository_name\SubGit\authors.txt 配置 SVN 中用户提交转换 GIT 提交后对应的用户名和邮箱，
＃   可将多个 SVN 帐号映射到一个用户名和邮箱，格式如下
＃   svnUser = Git User <user@example.com>
x:\> x:\SubGit-2.0.0\bin\SubGit.bat configure --svn-url [http://svn.domain.com/svn/svn_repository_name/ or file:///x:/svn_repository_name] SubGit_repository_name

# 执行安装
x:\> x:\SubGit-2.0.0\bin\SubGit install x:\SubGit_repository_name

# Git Bash 中输入，克隆一份 GIT 仓库，不含工作区，推送至指定 GIT 服务器
$ git clone SubGit_repository_name working-tree --bare
$ git remote add remote_name git@github.com/own_name/project_name.git
$ git push remote_name --all
$ git push remote_name --tags

```

## 二. 复杂情况

有哪些复杂情况？

### 1. 目录变更

前期 SVN 仓库创建时没有使用标准结构（trunk，branchs，tags），后期修改为标准结构，比如： `/svn/project_name/ <=> /svn/project_name/trunk …）`，想保留这些提交历史记录。

比较麻烦的就是这种情况，目前还没发现有哪些转换工具可以直接支持，这里通过一种变通的方式，即先把包含不正确结构历史记录的 SVN 仓库转换为都正确结构历史记录的 SVN 仓库。

用到几个 SVN 的命令：
```bat
# -r 1391:1391：指定导出范围
# --incremental 增量导出
# SVN 仓库存放路径
# 导出文件存放路径
x:\> svnadmin dump -r 1391:1391 --incremental x:\Repositories\project_name > x:\temp\svn-project_name-1391-1391-bak.dump

# 创建一个临时仓库，导入 SVN 记录
x:\> svnadmin create x:\Repositories\project_name_temp
# 原来是什么样，导入后还是什么样
x:\> svnadmin load x:\Repositories\project_name_temp < x:\temp\svn-project_name-1391-1391-bak.dump
# 将特定目录的提交分离出来
x:\> cat x:\temp\svn-project_name-0-1390-bak.dump | svndumpfilter exclude Documents --drop-empty-revs --renumber-revs > x:\temp\svn-project_name-0-1390-eDocuments-bak.dump
x:\> cat x:\temp\svn-project_name-0-1390-bak.dump | svndumpfilter include Documents --drop-empty-revs --renumber-revs > x:\temp\svn-project_name-0-1390-iDocuments-bak.dump
# 导入至目录：trunk/source，即 svn/abc <=> svn/trunk/source/abc
x:\> svnadmin load --parent-dir trunk/sources x:\Repositories\x:project_name_temp < x:\temp\svn-project_name-1391-1391-bak.dump

```

### 2. 有开发分支

在 SVN 仓库中有设 Develop 分支，比如：`svn/project_name/trunk（稳定分支）,svn/project_name/develop（开发分支）`

通过修改 SubGit 配置，可以实现转换后 GIT 包含 develop 分支。

{% codeblock 【config_001】 - x:\SubGit_repository_name\SubGit\config lang:bash http://subgit.com/book-remote/index.html#N202CE SubGit Remote Book%}
	…
	trunk = trunk:refs/heads/master
	branches = Develop:refs/heads/develop # 新增，Develop 指对应 SVN 目录（svn/project_name/Develop），注意大小写
	branches = branches/*:refs/heads/*
	tags = tags/*:refs/tags/*
	shelves = shelves/*:refs/shelves/*
	…
{% endcodeblock %}

### 3. 实例

#### 说明
项目A，托管在 Windows Server 安装的 VisualSVN Server 上，已有超过 1W+ commits，项目前期采用的结构为【1】。大概在 Commit Revesion：`1391-1394` 时，有位小伙伴意识到，我们应该用分支需要 Branchs，Tags，于是结构调整为【2】。再后来来了位新伙伴，说你们项目怎么没有 Develop 分支呢？于是调整新增了【3】：`svn/project_a/develop` 作为开发分支。

{% codeblock 【1】 - 项目初期 lang:bash %}
svn/project_a
	+ sources
	+ documents
{% endcodeblock %}

{% codeblock 【2】 - Commit Revesion 1391-1394 lang:bash %}
svn/project_a
	+ trunk
		+- sources
			+- documents
	+ tags
		…
	+ branchs
		…
{% endcodeblock %}

{% codeblock 【3】 - 新增开发分支 lang:bash %}
svn/project_a
	trunk
		sources
			documents
	+ develop
		+ sources
			+ documents
	tags
		…
	branchs
		…
{% endcodeblock %}

#### 操作

{% codeblock 实例 - 将 SVN 中所有提交历史记录，包括所有分支(branchs)和标签(tags)，迁移至 GIT。 lang:powershell %}

# 原仓库 x:\Reposities\project_a
# 1. 创建临时仓库
svnadmin create x:\Repositories\project_a_temp

# 2. 将目录改变的 Commits 提取出来 在临时仓库中顺序移至到了前面
svnadmin dump -r 1391:1391 --incremental x:\Reposities\project_a > x:\temp\svn-project_a-1391-1391-bak.dump
svnadmin load x:\Reposities\project_a_temp < x:\temp\svn-project_a-1391-1391-bak.dump

svnadmin dump -r 1394:1394 --incremental x:\Reposities\project_a > x:\temp\svn-project_a-1394-1394-bak.dump
svnadmin load x:\Reposities\project_a_temp < x:\temp\svn-project_a-1394-1394-bak.dump

# 3. 导出结构【1】时的 Commits，在临时仓库中重写 Commit是 为结构【2】
svnadmin dump -r 0:1390 --incremental x:\Reposities\project_a > x:\temp\svn-project_a-0-1390-bak.dump

# 3.1 分别过滤 & 提取 Document 文档的提交记录，并将其重写 svn/sources/Documents/ <=> svn/trunk/sources/Documents
cat x:\temp\svn-project_a-0-1390-bak.dump | svndumpfilter exclude Documents --drop-empty-revs --renumber-revs > x:\temp\svn-project_a-0-1390-eDocument-bak.dump
svnadmin load --parent-dir trunk/sources x:\Reposities\project_a_temp < x:\temp\svn-project_a-0-1390-eDocument-bak.dump

cat x:\temp\svn-project_a-0-1390-bak.dump | svndumpfilter include Documents --drop-empty-revs --renumber-revs > x:\temp\svn-project_a-0-1390-iDocument-bak.dump
svnadmin load --parent-dir trunk/sources x:\Reposities\project_a_temp < x:\temp\svn-project_a-0-1390-iDocument-bak.dump

# 4. 将剩下的 Commits 导出导入至临时仓库
svnadmin dump -r 1396:HEAD --incremental x:\Reposities\project_a > x:\temp\svn-project_a-1396:HEAD-bak.dump
svnadmin load x:\Reposities\project_a_temp < x:\temp\svn-project_a-1396:HEAD-bak.dump

# 5 SubGit 配置
x:\> x:\SubGit-2.0.0\bin\SubGit.bat configure --svn-url file:///x:/Reposities/project_a_temp x:\Reposities\SubGit_project_a


# 5.1 编辑开发者映射列表 SubGit_project_a\SubGit\authors.txt
# 5.2 编辑SubGit配置【config_001】，支持 Develop 分支导入
	
# 6. 执行安装，漫长的等待…
x:\> x:\SubGit-2.0.0\bin\SubGit install x:\Reposities\SubGit_project_a
	
# 7. 使用 Git Bash，克隆一份 GIT 仓库，不含工作区，推送所有分支（branchs）和标签（tags）至指定 GIT 服务器
$ git clone SubGit_project_a working-tree --bare
$ git remote add remote_name git@github.com/own_name/project_a.git
$ git push remote_name --all
$ git push remote_name --tags

{% endcodeblock %}

##写在最后
1W+ Commits 的 SVN 仓库迁移至 GIT 大概需要4-6个小时（源代码100Mb，仅当参考，但这已经比 GIT 提供的 `git svn` 命令不知要快多少倍了 ）， 当完成迁移后为安全起见，我们还需要对源代码做一次校验，即，捡出 SVN 最新代码（`svn/project_a/develop`）和 GIT 最新代码（`git clone git@gitserver.com project_a -b develop`），让后使用 [`BCompare`](http://www.scootersoftware.com) 或类似工具做一次差异比较，确认所有源代码无差异，这才恭喜你，完成了迁移。

由于 GIT 的学习有一定曲线，如果小伙伴开发团队比较大（20+ 人），小伙伴们对 GIT 接受程度肯定有所差异，为了减少迁移至 GIT 对大伙的影响，可以考虑采用 SubGit 提供的方案，同时支持 SVN 和 GIT 双向提交。

由于作者最开始这么做已经是半年前的时候（当时 SubGit 还是 `v1.x.x` 现在都 `v2.0.0`），其中 SubGit 配置和安装部分直接替换为 `v2.0.0` 用法。实例部分直接参考之前生产环境操作记录，重要部分也经过测试，但个人精力测试范围有限，如有遗漏或异常欢迎留言和反馈。

