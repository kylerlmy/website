---
title: Hexo + GitHub 建站指北
date: 2019-07-25 20:23:39
tags: Hexo
---

### Hexo 是什么？
Hexo 是一个[开源](https://github.com/hexojs/hexo)、简单、轻量级，而且很强大的博客框架，主要使用 Nodejs 实现；非常适合喜欢使用 Markdown 文档来写博客的伙伴；它通过将 Markdown 文件转换为静态网页，来实现 Web 访问。它还支持很多可以定制功能，如:插件（Plugin）和主题（Themes），可以根据自己的喜好进行个性化设置。[这里是他的文档](https://hexo.io/zh-cn/docs/index.html)

### Hexo 的建站步骤

- [安装 Git](https://hexo.io/zh-cn/docs/)
+ [安装 NodeJs](https://hexo.io/zh-cn/docs/)
- [Github 创建项目](https://juejin.im/post/5c4dac03f265da613c0a2811#heading-15)

### 需要注意的地方

1. 在 GitHub 为个人的 Blogs 内容创建仓库时，需要创建两个分支 master 和 gh-pages ,master 用于存放生成文档，gh-pages 用于存放部署文档。

2. 由于在 Github 上创建项目仓库时，GitHub pages 默认访问路径为  *”http(s)://username.github.io/projectname“* 所以在设置根目录下的配置文件 *”_config.ymal“* 中的 url 配置项和 root 配置项时，需要将 url 设置为 *”http(s)://username.github.io/projectname“*，并把 root 设置为 *”/projectname/“*，而非默认的 *”/“*
3. [_config.ymal 配置文件内容](<https://github.com/kylerlmy/website/blob/master/_config.yml>)

