---
layout: post
title: 如post ceshi
tags:
- Jekyll
- Jacman
categories: Jek
description: Jacman 是一款扁平化，有着响应式设计的 Jekyll 主题。本站正式使用了 Jacman 主题。Jacman 基于 Jacman 的 Hexo 主题修改而来。你可以前往本站和 Demo 预览更多关于本主题的更多效果。如果你有任何问题或意见欢迎到 GitHub 发表 issue。
---
## 主题介绍
Jekyll-Jacman 是为 [Jekyll](http://jekyllrb.com) 设计的一款清新且具有响应式的主题，拥有更丰富的特性并支持了很多的国内服务。Jacman 始于 [Jacman](https://github.com/wuchong/jacman) 移植而来。

<!-- more -->

## 配置指南

Jacman 主题提供了丰富的配置属性，可以实现您对主题的自定义。配置文件`_config.yml`位于主题根目录下。本次更新对配置文件进行了较大调整，如您之前就使用了 Jacman，也需要您根据以下指南进行相应的修改。


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为主题中已经内置了这两个页面的模板，所以他们会被正确的解析出来。

- **控件 widgets**
提供了7种小工具。包括标签、分类、RSS、友情链接、微博秀。

 **友情链接**：友情链接的网址添加可以在`links`属性下添加。
 
 **微博秀**：需要注意的是，如果要启用微博秀，您必须填上`author`属性下`tsina`和`weibo_verifier`的值，前者是您微博ID，后者是您微博秀的验证码，访问 http://app.weibo.com/tool/weiboshow 在如下图位置，可以获得您的 verifier，如：我的是`b3593ceb`。
![](http://ww1.sinaimg.cn/large/81b78497jw1emegd6b0ytj209204pweu.jpg)

 如果要关闭侧边栏，将`close_aside`置为`true`，就会在博文页面自动关闭侧边栏。

- **图片相关 Image**
本主题可以设置网站相关图片，例如网站图标（`favicon`）、网站logo（`imglogo`）、作者头像（`author_img`）。建议启用网站logo，格式建议为`.svg`或`.png`格式。同时建议提供配套的 favicon 以及在苹果设备上的图标`apple_icon`（背景不要透明）。

- **首页显示模式 Index**
目前首页的显示模式支持两种，一种是原先的卡片式（前往 [Demo](http://wuchong.me/jacman) 预览），另一种是类似官方主题的文章展开式（[本站](http://wuchong.me)即采用的这种）。两者各有优劣，前者首页加载速度更快，后者文章内容更能吸引读者。主题默认采用后一种展开式，如需开启第一种卡片式，请设置`index`属性下的`expand: false`。

 卡片式的文章摘要是截取文章内容的前140个字，也可以自己总结`description`并将其放在开头的`front-matter`中。展开式的文章摘要就是使用`<!-- more -->`截取了。

- **作者信息 author**
作者信息，建议尽量填写完整。其中`tsina`是你的新浪微博ID，不同于用户名或微博主页地址。启用这个属性后，其他用户在微博上分享你文章的同时会自动@你。同时它和`weibo_verifier`一起作用生成微博秀。`intro_line1`和`intro_line2`是网站底部的个人介绍。`weibo`、`twitter`、`facebook`等是用来显示网站右下角的社交按钮的，如下图所示。
![](http://ww4.sinaimg.cn/large/81b78497jw1emgscr3575j2078050jrc.jpg)

- **目录 toc**
是否启用在文章中或侧边栏中的目录功能。二者可以都为`true`或都为`false`。同时，如果你希望在特定的某一篇文章中关闭目录功能你可以在文章文件开头中的`front-matter`中加上一行`toc: false`。

- **评论 comments**
填写`duoshuo_shortname`[多说](http://duoshuo.com/)的用户名，启用多说评论系统。在大陆地区更好用的评论系统。

 填写`disqus_shortname`[disqus](http://disqus.com/) 的用户名，启用 disqus 评论系统。国际上更广泛使用的评论系统。设置博客根目录下的`_config.yml`文件中的`disqus_shortname`同样也能开启该功能。

- **加网分享 jiathis**
[加网](http://www.jiathis.com/)分享系统。默认关闭，因为主题已经内置了原生的分享功能。

- **网站统计 Analytics**
`google_analytics`：Google Analytics追踪代码。请注意：Google Analytics已经升级到了Universal Analytics。请先前往后台升级你的Google Analytics版本后再启用追踪代码，更多信息请[点击这里](https://developers.google.com/analytics/devguides/collection/upgrade/?hl=zh_CN)了解。

 `baidu_tongji`：百度统计功能。需要填写站点特征码`sitecode`，在[官网](http://tongji.baidu.com/web/welcome/login)注册并配置站点后，获取特征码。特征码可以在「网站中心」-> 「代码获取」中查看，如下图所示的`e6d1f421bbc9962127a50488f9ed37d1`，注意去掉前面的`3F`。
![](http://ww4.sinaimg.cn/large/81b78497jw1emf4v6qf91j20kf07sq8v.jpg)

 `cnzz_tongji`：站长统计功能。需要填写站点ID`siteid`，同理在[站长官网](http://www.cnzz.com)注册并配置站点后获得。

- **数学公式 mathjax**
主题支持写 LaTex 数学公式。只需要在文章文件开头的`front-matter`中，加上一行`mathjax: true`，即可在文中写 LaTex 公式。

- **图片浏览 fancybox**
默认关闭，如果你经常发表 Gallery 类型的文章，那么请设置为`true`。

- **返回顶部 totop**
右下角`返回顶部`按钮，默认开启。

- **自定义搜索 Search**
`baidu_search`：如果开启百度站内搜索需要登录 [百度站内搜索](http://zn.baidu.com/)，配置好你的站点，并开启站内搜索获取搜索ID，另外`site`属性可以填默认值，也可以填自己做了CNAME的二级域名，更详细的可以阅读[这篇博客](http://gengbiao.me/hexo/hexo%E6%B7%BB%E5%8A%A0%E7%99%BE%E5%BA%A6%E7%AB%99%E5%86%85%E6%90%9C%E7%B4%A2/)了解。

 `google_cse`：如果开启谷歌自定义搜索需要先登录 [Google CSE](https://www.google.com/cse/)，配置好你的站点，并获得此自定义搜索的ID。此外你需要在博客目录下的`source`文件夹中建立`search`文件夹并包含一个`index.md`文件。内容为：
 ```
 layout: search
 title: search
 ---
 ```

 `tiny_search`: 如果要开启[微搜索](http://tinysou.com/)，需要先注册一个帐号，配置一个Engine，将Engine的Key填入配置文件中的`id`即可。


##常见问题
- **Q：图片默认都是居左的，我怎么设置能让图片居中呢？**
>使用 `<img src="" style="display:block;margin:auto"/>`的HTML标签。

- **Q：如何建立一篇图片类文章（Gallery Post）？**
> 直接新建一个 Markdown 文件，将其`front-matter`修改为如下，即可看到主题为图片类文章提供的样式。
```