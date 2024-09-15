---
layout: post
title:  "no style, please!"
date:   2024-09-15
categories: blog
---

前面已经介绍了把个人网站部署在 Github Pages，并给 Github Page 配置定制化域名的流程。考虑到个人信息安全与审查风险，我将在 Aliyun 购买的域名转移到了 [Cloudflare](https://www.cloudflare.com/)（可以在 [ICANN](https://lookup.icann.org/en) 查看域名注册信息）。为了进一步加快网站部署流程，这里引入一个静态网站生成工具 Jekyll，以及 Jekyll 主题 no style, please! 的应用介绍。

### What is Jekyll
[Jekyll](https://jekyllrb.com/) 是一款简单的静态网站生成器。它可以将纯文本转化成静态网站，你只需要用自己习惯的标记语言如 Markdown 来编写博客文章，借由 Jekyll 预设模版内置的文章发布、分类、标签、归档等博客所需的功能，使创建和维护博客变得更加简单和高效。

[no style, please!](https://riggraz.dev/no-style-please/) 是一款几乎没有 CSS 样式的，快速生成，极简的 Jekyll 主题。

结合了这套组件，网站的发布和维护变得非常简单，网站所有者只需专注于输出内容，通过几个简单的命令行，文章就自动部署到网站。

下面来一步一步演示它是如何实现的。

### Installation
Jekyll 是一个 Ruby 项目，安装 Jekyll 首先需要 Ruby 的开发环境。

#### Install chruby and ruby-install with Homebrew

```bash
brew install chruby ruby-install xz
```

#### Install the latest stable version of Ruby

```bash
ruby-install ruby 3.1.3
```

#### Configure your shell to automatically use chruby

```bash
echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.1.3" >> ~/.zshrc # run 'chruby' to see actual version
```

#### Install Jekyll

```bash
gem install jekyll bundler
```

#### Create a new Gemfile

```bash
bundle init
```

#### Edit Gemfile

```bash
gem "jekyll", "~> 4.3.3"
# Change default Jekyll theme to no-style-please
gem "no-style-please"
```

Run `bundle` to install jekyll for your project.

### Create a site
在 _data, _includes, _layouts 中预设网站模版样式，将 Markdown 格式的文件放入指定的 _posts 路径。

### Build your site

```bash
bundle exec jekyll serve
```

通过 `localhost:4000` 预览生成后的网站，确保无误后将生成后的网站内容推到 Github 触发 Github Pages 部署，至此网站发布成功。
