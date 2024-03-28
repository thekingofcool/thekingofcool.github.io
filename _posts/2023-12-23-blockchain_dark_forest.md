---
layout: post
title:  "区块链黑暗森林漫游指南"
date:   2023-12-23
categories: reading
---

区块链是个伟大的发明, 它带来了某些生产关系的变革, 让“信任”这种宝贵的东西得以部分解决。不需要中心化、不需要第三方角色, 有些“信任”基于区块链就可以得到很好解决, 不可篡改、按约定执行、防止抵赖。

但不是每个人对区块链都有着清晰的理解。这导致有邪念的人能轻易钻技术的空子, 将黑手伸进人们的钱包, 造成资金损失, 且几乎不能挽回。

在区块链黑暗森林世界里, 需要牢记两大安全法则:

零信任。始终保持怀疑。
持续验证。培养能力去验证你的怀疑, 并把这种能力养成习惯。
创建钱包
区块链中你的身份其实指的就是公钥-私钥对, 公钥是你对外的账号, 而私钥类似于密码, 只有你本人知道, 是一切区块链活动的核心, 私钥如果丢失/忘记或者被第二个人知道, 那这个身份就不再(唯一)属于你。

钱包是你与区块链交互的媒介。

从应用分类来说钱包主要包括几种: PC钱包、浏览器扩展钱包、移动端钱包、硬件钱包及网页钱包等。

从触网与否来说主要可以分为冷钱包和热钱包。

理论上没有绝对安全的系统, 只要连接(过)外界设备都存在信息泄漏风险。这里我们假设业内使用最多的平台所提供的钱包相对安全, 但无论如何都是要自己亲身对比作出选择。但对于大多数人来说, 只要正确找到官网就避免了大部分资金丢失, 利用Google或通过其他信得过的平台/人官方提供的链接, 最好交叉认证之后再进行授权/交互。对于频繁使用的钱包不存放过多的资金, 大多数不常会动用的可以放在冷钱包, 前提是对于冷钱包概念清晰, 以及钱包助记词安全保存的基础上。

但即使是官网提供的下载链接, 也存在源头被完全篡改的可能(官方自己内部作恶、内部被黑、官网被入侵替换了相关信息等等), 验证的方式是文件一致性判断, 和项目方提供的发布验证信息进行对比。

助记词
一般来说, 我们创建了钱包后, 直接打交道的关键信息是助记词(而不是私钥), 毕竟助记词是方便人类记忆的。助记词是有标准约定的(如BIP39), 这就对助记词提了要求, 比如一般 12 个英文单词, 也可以是其他数量(3 的倍数), 不过不会超过 24 个单词, 要不然太复杂也就不助记了, 数量少于 12 的话, 安全性也不靠谱, 12、15、18、21、24 都好说。不过从业内习惯来说, 一般流行的是 12 位, 安全性足够, 有的硬件钱包安全标准更高。还有除了英文单词, 也可以是其他的, 比如中文、日文、韩文等等。但也不是什么单词都可以, 有一个固定 2048 个单词列表, 具体参考: bip-0039 Wordlists

创建钱包时, 助记词的出现是非常敏感的, 请留意你身边没有人、摄像头等一切可以导致偷窥发生的情况。同时留意下助记词是不是足够随机出现, 正常来说这些知名钱包生成的助记词随机数是绝对足够的, 以防万一呢？你真的很难知道, 拿到手的钱包到底有没有万一的猫腻。最后, 当你准备把该钱包当成冷钱包使用时, 确保钱包创建前和创建后永不触网。

自由
其他就不再多说了, 不可信的链接不要点, 助记词备份和使用也是一门艺术, 在区块链中犹如在黑暗森林, 尽可能久地隐藏自己的物理身份才能获得足够的自由。