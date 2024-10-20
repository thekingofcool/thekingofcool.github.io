---
layout: post
title:  "My Password Management Strategy"
date:   2024-04-01
categories: blog
---

随着现代人网上冲浪时间越来越长，有一件事显得愈发重要，但又常常被人忽略，那就是各类网站的密码管理。

如今我们注册的网站品类纷繁复杂，有的类似新闻资讯，账密丢失被盗都无足轻重，有的则涉及个人财产或社交网络，一旦被盗会给自己或朋友带来很多困扰。好在目前一些涉及到财务的网站或应用会开启设备的二次验证，但也不能涵盖我们上网情景的方方面面。

有人为了避免密码遗忘，把所有平台的密码设置成同一个，这样账号（通常是手机号码或邮箱）密码只要在一个平台被泄露，带有不良意图的技术人员就可以去各种网站 **“[撞库攻击](https://baike.baidu.com/item/%E6%92%9E%E5%BA%93/16480882)”**。泄露的原因当然不止是自身无意暴露，当你提交注册一个网站，包含你个人信息的表单就被提交到对方服务器中，对于你来说对方是否遵守职业操守，是否合理保管你的密码，甚至是否会对你的个人信息加密存储都不一定。所以，所有平台使用一种账号密码无疑是一件十分危险的事。

那么如果你给每个平台都设置一个独特的密码，又应该如何去管理这些信息？一个选项是交给业内成熟的密码管理工具，不少人熟知的 [1Password](https://1password.com/) 就是其中之一，这个工具我使用过一年，各平台同步，自动填充账号密码，确实方便。可当你对个人信息极度敏感，1Password 这种商业公司也无法让你完全信任，那应该怎么办？

首先针对密码管理我们要知道，我们唯一需要的功能就是一个加密的数据库，用以保管我们的所有密码，1Password 承担替我们保管这个加密数据库的职责，并收取年费。那么如果有一个开源的技术，将这个加密数据库放在我们自己的磁盘，通过记住一个密码就可以访问所有密码，而且又免费，是不是就很完美呢？

好在我们处在互联网，**你的 99% 的需求都曾经有其他人提出，并且被具有开源精神的极客们满足了**。所以问题就在于知道自己想要什么，并能获取到需要的信息。

### KeePassXC
[KeePassXC](https://keepassxc.org/) 这款工具满足了以上大部分需求，通过它，你可以在本地创建一个后缀 .kdbx的文件作为密码数据库，通过桌面客户端打开文件，输入预制的一段密码，即可操作其中的 entry。在这个工具中，一个文件相当于一个 Database，一条账密信息作为一个 Entry，可以通过配置自动备份一个本次修改以前的文件。

在电脑桌面端，可以在主流浏览器安装相应[插件](https://keepassxc.org/download/#browser)， 并通过对插件的[配置](extension://pdffhmdngciaglkoonimfcmckehcpafo/options/options.html#general-settings)，当 Database 文件处于解锁状态时，浏览器访问到特定页面可以自动填充账号密码。

唯一鸡肋的一点就是在移动端暂时没有支持。

通过 KeePassXC + 主流云盘（OneDrive、Google Cloud、iCloud、DropBox）配合使用，可以完成跨平台密码同步。

说到云平台，我们可以如何最大程度利用各大厂商免费云盘为自己服务的同时又保证自己的隐私得到保护呢？

### Cryptomator
[Cryptomator](https://cryptomator.org/) 是一个使用符合最新标准的加密技术，对本地或云上数据进行加密的工具。正如其官网描述的那样： **Put a lock on your cloud**.

> With Cryptomator, the key to your data is in your hands. Cryptomator encrypts your data quickly and easily. Afterwards you upload them protected to your favorite cloud service.

你需要指定一个路径作为 vault，通过输入密码进入 vault，你会看到一个标准的文件系统，在其中你可以放置你的个人文件，而对于服务器管理员或有权限读取当前电脑文件系统的程序而言，所有的内容都将只是无法理解的加密文件。

Give it a try, you won't regret it.
