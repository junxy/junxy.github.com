---
layout: post
title: "新款 MacBook Pro 无法连接 2.4G WiFi？"
date: 2019-05-29 22:52
comments: true
categories: Fixes Mac
---

## 问题的发现

尝试优化 5G WiFi 传输速率时，发现我的 MacBook Pro(2016 Multi-Touch Bar 高配版) 无法连接 2.4G 的 WiFi。一开始是输入正确的密码，也无法连接，之后是连输入密码这一步都到不了，直接提示无法连接。用其他设备可以正常连接 2.4G WiFi，可以排除，不是路由器的问题。

![](/images/2019/5/WX20190529-225620@2x.jpg)

以前没有发现这个问题，还以为是 macOS Mojave 系统问题，重启系统、更新系统版本到 `10.14.5 (18F132)` 问题依旧。

## 原因

查了不少资料，中文很少有说到点，最后用英文关键词搜索 `macos mojave cannot connect 2.4g` 应该是找到了答案：**USB3.0 会干扰 2.4G WiFi**。

![](/images/2019/5/WX20190529-225621@2x.png)
Ref: <https://forums.macrumors.com/threads/wifi-issues-with-new-mbp-on-2-4ghz.2064959/#post-27194251>

赶紧验证一下：

- 拔掉 `Type-C to USB 转接口`(相当于断掉所有外设)，重连 2.4G WiFi，秒连 ✅
- 插上 `Type-C to USB 转接口` + `USB2.0 的机械键盘`，重连 2.4G WiFi，秒连 ✅ (也排除，不是转接口的问题)
- `Type-C to USB 转接口` + `USB3.0 移动硬盘`，无法连接 2.4G WiFi ❌
- `Type-C to USB 转接口` + `USB3.0 Hub(不连接其他设备)`，也无法连接 2.4G WiFi ❌ (特意拆开 Hub，看了一下里面有电路和芯片，即使不连接其他外设也会激活 USB3.0)

**基本可以确认，使用 USB3.0 时会严重干扰 2.4G WiFi（尤其是 WiFi 连接认证的时候）**

## 解决方案

1. 尽量使用 5G WiFi（推荐），（BTW：有时候 5G WiFi 也有可能会被干扰到，主要是在连接认证的时候，碰到过1次）
2. 连接 2.4G WiFi 认证时，拔掉外设，连上之后再插回去。(我试了下，连上后可以正常上网，但稳定性不好说，时间长不知道会不会掉线重连)
3. 用 USB 2.0，像键盘是可以，没什么影响，移动硬盘用 USB2.0 就太慢了
4. 给 USB 3.0 线和设备增加更好的屏蔽层（理论上应该可以，但有些不切实际）

后面用中文搜索关键词 `macos mojave 无法连接 2.4g`，发现知乎 <https://www.zhihu.com/question/52372614> 也有提到这个问题。

这个问题应该是个设计问题，至少目前 Apple 公司无法解决（2016/2017 MacBook Pro，都有这个问题，会不会解决还不清楚），可能和推出 Type-C 接口有很大关系。

## 相关参考：

- <https://discussions.apple.com/thread/8440574?answerId=33563118022#33563118022>
- <https://www.intel.com/content/www/us/en/products/docs/io/universal-serial-bus/usb3-frequency-interference-paper.html>
- <https://apple.stackexchange.com/questions/329970/usb-c-dongles-killing-wifi-on-2016-2017-macbooks-who-s-to-blame>

## 最后分享两个 WiFi 相关的

1.在 macOS 中点击 WiFi 图标时，按住 `option` 键，可以查看到详细的 WiFi 连接信息
![](/images/2019/5/WX20190529-225622@2x.png)

2.关于 5G WiFi 的优化的方法之一，使用 DFS 频道。所谓 DFS 频道，就是不同国家和地区限制部分 5Ghz 频道的使用，但符合相关设定也可以使用。

如果所在环境 5G WiFi 非常多的话，频道不够用，可以考虑使用 DFS 频道减少干扰（在有些国家和地区，不恰当使用可能违法。要遵守，在室内，在限制的发射功率内使用，避免干扰公共设施）。

有些路由器系统可以开启所有支持的频道，我实际测试下来，使用 DFS 频道效果不明显，不知道是不是兼容问题。不过周围 5G WiFi 频道不多，干扰不大，就先这样了。

![](/images/2019/5/WX20190529-225623@2x.png)
![](/images/2019/5/WX20190529-225624@2x.png)

了解 DFS 更多：

- <https://netbeez.net/blog/dfs-channels-wifi/>
- [Wikipedia:WLAN信道列表#5_GHz_(802.11a/h/j/n/ac)[11]](https://zh.wikipedia.org/wiki/WLAN%E4%BF%A1%E9%81%93%E5%88%97%E8%A1%A8#5_GHz_(802.11a/h/j/n/ac)[11])

优化 WiFi 的时候可以借助像 `WiFi Explorer` 的 App 来帮助分析所在环境 WiFi 频道使用情况
![](/images/2019/5/WX20190529-225625@2x.png)


关键词： Mac、WiFi、DFS