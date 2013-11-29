---
layout: post
title: "ASP.NET web application automated deployment using MS WebDeploy - server-side configuration - part 1"
date: 2013-11-29 23:14
comments: true
categories: tech .net webdeploy
---
# 使用 MS WebDeploy 自动化部署 ASP.Net Web 程序 - 服务端配置（上）#
## 一、环境配置 ##

1. 安装 Windows Server（Current 2012）、配置IIS（Google 一大把，这里不做介绍了…）
2. 安装 Web Deploy （Current 3.5），使用 Web 平台安装程序。

![选择安装0](http://ww4.sinaimg.cn/large/015c2b7dgw1eb1ouygmyxj20fd0e90uh.jpg)

首次使用 Web 平台安装程序，会提示并要求安装

![选择安装1](http://ww3.sinaimg.cn/large/015c2b7dgw1eb1n9eowg8j20oo0gqacp.jpg)

选择安装  Web Deploy，URL重写工具（可选）

![安装完成](http://ww1.sinaimg.cn/large/015c2b7dgw1eb1n9f47w8j20jc0c8ac2.jpg)

安装完成！

![确认安装0](http://ww1.sinaimg.cn/large/015c2b7dgw1eb1n9fu8djj20fc04bdg4.jpg)

确认安装是否成功

![确认安装1](http://ww2.sinaimg.cn/large/015c2b7dgw1eb1omkd3hcj20gp08gabc.jpg)

看到管理委派默认配置，恭喜你，环境配置完成 ；）

![确认安装2](http://ww2.sinaimg.cn/large/015c2b7dgw1eb1omksxmrj20qb09s75v.jpg)

安装 Web Deploy 默认会创建这两个账户，不要改动他

## 二、配置网站支持 Web Deploy ##
1. 创建 Web Deploy 网站发布用户账号，支持Windows账户和IIS用户账号，这里我们选择使用IIS用户账号。

![创建账号0](http://ww4.sinaimg.cn/large/015c2b7dgw1eb1pjcd5hyj20ky0c6dhu.jpg)

![创建账号1](http://ww2.sinaimg.cn/large/015c2b7dgw1eb1pbixd7jj20d009vwf4.jpg)

添加用户，要求设置强密码

2.设置站点允许使用 Web Deploy 作为发布方式

![启用发布0](http://ww2.sinaimg.cn/large/015c2b7dgw1eb1pao9duhj20eq0jngoa.jpg)

![启用发布1](http://ww1.sinaimg.cn/large/015c2b7dgw1eb1paoosf8j20ds0dot9w.jpg)

选择之前创建的 IIS 用户账号，其他可以不用设置

![启用发布2](http://ww4.sinaimg.cn/large/015c2b7dgw1eb1q0vbq1vj20dr0dmwg4.jpg)

设置，当看到类似结果，恭喜你，服务端配置到此完毕！

接下来我将介绍 使用 Web Deploy 部署项目的部署配置，最终通过结合 Jenkins CI 达到自动化部署产品，敬请期待～ ；）
