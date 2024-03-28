---
layout: post
title:  "如何将网站托管于去中心化服务器"
date:   2023-12-24
categories: blog
---

通常来说, 想要搭建一个网站, 首先需要租用一个云服务器, 云服务厂商大同小异, 如果选择中国大陆的云厂商还需要额外的ICP(Internet Content Provider)备案和公安备案。

中心化的云服务器(AWS, Azure, etc.), 乃至部分打着去中心化概念的去中心化存储协议(IPFS), 其本质也是存储提供商购买的云厂商服务, 这里将后者这种内容物联网定义为伪去中心化。这两类模式最核心的缺点在于内容隐私性和抗审查性的缺失。

## What is Swarm
Swarm是一个去中心化存储和内容分发的技术, 类似于一个服务器分散在全球的AWS, 其内生一套激励机制鼓励机器提供为网络存储和带宽, 用户则需要为自己存储的内容购买支票, 按存储时长和内容大小付费, Swarm利用技术做到存储提供商对存储的真实内容一无所知, 是自由主义精神的极致体现。

今天我们尝试在这样一套去中心化网络上发布自己的网站。将网站内容托管于Swarm Network, 利用Swarm官方提供的Gateway node作为门户将网站提供给网上冲浪者。

## Publish a website
首先在自己电脑上下载Swarm Desktop作为接入Swarm Network的一个light node, 与Swarm上托管的内容进行交互。

安装好客户端之后该节点会自动创建一个以太坊钱包地址, Swarm Network部署在以太坊侧链Gnosis Chain上, 该链的基础代币是xDAI, 要想在这个链上交互, 首先我们需要在自己的钱包中从Ethereum Mainnet上将DAI跨链至Gnosis Chain成xDAI, 再将xDAI转入Swarm节点钱包, 你也可以在本地Swarm Desktop安装包中找到这个节点钱包的私钥信息, 将其导入Metamask, 直接对该钱包进行操作。

MacOS你可以在:
`/Users/[your_username]/Library/Application Support/Swarm Desktop/data-dir/keys/`
中找到swarm.key的json文件, 在Metamask中选择add account - import account, select type选择JSON File, 选择刚刚找到的swarm.key文件, 导入文件需要输入密码, 这时从:
`/Users/[your_username]/Library/Application Support/Swarm Desktop/config.yaml`
中拿到password, 节点钱包导入完成。将钱包网络切到Gnosis Chain应该就可以看到之前转入的xDAI。利用Honeyswap将一部分xDAI转为xBZZ作为后续储存费的货币。
在Swarm Desktop dashboard中, 选择Account - Stamp, Buy New Postage Stamp, 提前购买所需存储服务的内容大小以及存储时间, 并支付相应的xBZZ。

再选中File, 在Upload中选择Add Website, 选中自己的网站文件夹, 选择上传。这时需要用到之前购买的postage stamp, then proceed with that stamp, 上传完成之后会有一个Swarm hash, 通过这个hash, 你可以在Swarm Network中找到对应的资源。

因为安装Swarm Desktop, 你的电脑本地运行着一个Swarm Bee light node, 因此, 简单在浏览器中输入:
`http://127.0.0.1:1633/bzz/[your_resource_swarm_hash]/`
就可以访问之前的网站。

## 绑定ENS Domains
如果你拥有ENS Domains, 你可以将这个Swarm hash关联到你的ENS域名上, 在Edit profile中Add profile fields选择Website, 在Swarm中输入:
`bzz://[your_resource_swarm_hash]`
点击确认这时你需要将该信息上链至以太坊主网, 需要支付的gas费和你提交时的网络拥堵情况以及你所同步的内容条数有关。

## Welcome to the club
至此, 在浏览器键入:
`https://api.gateway.ethswarm.org/bzz/[your_ens_domain]/`
即可访问你的去中心化网站。
