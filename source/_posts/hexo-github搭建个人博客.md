---
title: hexo+github搭建个人博客
date: 2019-08-30 16:40:24
tags:
	- hexo
categories:
	- 建站
---

# Hexo+github搭建个人博客

## 环境准备

- node.js
- git

安装hexo

```
npm install hexo-cli -g
```

## 利用hexo搭建一个博客

**创建博客目录qin-k.github.io**

```
hexo init qin-k.github.io
cd qin-k.github.io
npm instal
```

**运行**

- hexo clean: 清除缓存,删除生成的pulic目录文件

- hexo g(generate): 生成静态页面

- hexo s(server): 运行

  打开浏览器，输入地址 **localhost:4000** 即可看到效果 

## 基本配置及美化

网站的设置大部分都在项目录入`_config.yml`文件中, 主题配置在themes主题目录下对应主题下的`_config.yml`。

官方文档:<https://hexo.io/zh-cn/docs/configuration.html>

**注:**修改配置后需要`hexo clean`清除缓存重新`hexo g`生成静态页面。

### 网站配置

基本配置

```yaml
# Site
title: 秦凯的博客
subtitle: 笨鸟先飞
description: 这是个人博客
keywords: 个人,技术,博客
author: 秦凯
language: zh-CN
timezone:
```

### 主题配置

使用其他主题，如next

**下载主题**

```
Git clone https://github.com/theme-next/hexo-theme-next themes/next
```

**网站配置中使用next主题**

```
theme: next
```

设置菜单使用tags和categories

首先在主题配置中menu处取消注释:

```yaml
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

创建对应页面:`hexo new page "tags"`

会在source目录下生成categories目录其中包含index.md 文件。修改此文件

```yml
title: tags
date: 2019-08-29 22:57:41
type: "tags"
comments: false
```

categories和about等的添加于此类似

### 主题美化

**修改网站图标**

将16x16和32x32的图标文件传到`themes\next\source\images`下。

修改主题配置文件。

```yaml
favicon:
  small: /images/favicon16x16.png
  medium: /images/favicon32x32.png
  apple_touch_icon: /images/apple-touch-icon-next.png
  safari_pinned_tab: /images/logo.svg
  #android_manifest: /images/manifest.json
  #ms_browserconfig: /images/browserconfig.xml
```

**设置头像**

`themes/next/_config.yml`中设置：在source/images/avatar.png添加头像。

```yaml
# Sidebar Avatar
avatar:
  # In theme directory (source/images): /images/avatar.gif
  # In site directory (source/uploads): /uploads/avatar.gif
  # You can also use other linking images.
  url: /images/avatar.png
  # If true, the avatar would be dispalyed in circle.
  rounded: true
  # If true, the avatar would be rotated with the cursor.
  rotated: false
```

**设置Scheme**

选择自己喜欢的scheme

```yaml
# Schemes
#scheme: Muse
#scheme: Mist
scheme: Pisces
#scheme: Gemini
```

**修改footer添加访客人数和总访问量**

在`footer.swig`中添加标签，同时我们还可以通过修改主题配置来方便选择是否显示访问量，步骤如下：首先，我们在next主题下的`_config.yml`中加入以下配置：

```yaml
footer:
	...
    # visitors count
    counter: true
```

以更改counter的值为false来隐藏页脚的访问量统计，然后，我们在`themes/next/layout/_partial/footer.swig`中添加以下代码：

```
{% if theme.footer.counter %}
    <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
    <span id="busuanzi_container_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
    <span class="post-meta-divider">|</span>
    <span id="busuanzi_container_site_uv">本站访客数<span id="busuanzi_value_site_uv"></span>人</span>
{% endif %}
```

**设置社交链接**

```yaml
social:
  GitHub: https://github.com/Qin-K || github
  E-Mail: mailto:qinkkai@foxmail.com || envelope
  Weibo: https://weibo.com/wqinkai || weibo
  #Google: https://plus.google.com/yourname || google
  #Twitter: https://twitter.com/yourname || twitter
  #FB Page: https://www.facebook.com/yourname || facebook
  #VK Group: https://vk.com/yourname || vk
  #StackOverflow: https://stackoverflow.com/yourname || stack-overflow
  #YouTube: https://youtube.com/yourname || youtube
  #Instagram: https://instagram.com/yourname || instagram
  #Skype: skype:yourname?call|chat || skype
```

**修改文章内容宽度**

修改主题目录下`source/css/_schemes/Picses/_layout.styl`

```css
$white_max_width = 1200px

header{ 
  width: 90% !important;
  max-width: $white_max_width;
}
header.post-header {
  width: auto !important;
}
.container .main-inner {
  width: 90%;
  max-width: $white_max_width;
}
.content-wrap { width: calc(100% - 260px); }


.header {
  +tablet() {
    width: auto !important;
  }
  +mobile() {
    width: auto !important;
  }
}


.container .main-inner {
  +tablet() {
    width: auto !important;
  }
  +mobile() {
    width: auto !important;
  }
}

.content-wrap {
  +tablet() {
    width: 100% !important;
  }
  +mobile() {
    // 为了在手机访问时，内边距不至于太大
    padding: 0 !important;
    width: 100% !important;
  }
}
```

### 文章相关配置

**修改文章部分显示**

对于未显示部分，点击阅读全文可以查看

```yml
auto_excerpt:
  enable: false
  length: 150
```

**修改文章模板**

修改`source/scaffolds/post.md`

```yml
title: {{ title }}
date: {{ date }}
tags: 
categories: 
description: 
```

**添加字数统计、阅读时长**

安装插件

```
npm install hexo-symbols-count-time --save
```

修改项目配置文件

```yml
symbols_count_time:
  symbols: true
  time: true
  total_symbols: true
  total_time: true
```

**添加来必应评论**

去[来必应官网](https://www.livere.com)注册，获取data-uid

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190830154138.png"/>

修改主题配置文件填写自己的uid

```
livere_uid: MTAyMC8...Mi8xMzM4OA
```

## 部署到github

首先注册一个github账号，并创建一个名为\<youname\>.github.io的仓库。如我的个人仓库:`qin-k.github.io`

**安装hexo-deployer-git插件**

```
npm install hexo-deployer-git --save
```

**网站配置git**

在网站配置文件中配置

```
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
```

- repo: 仓库地址

- branch: 分支

- 部署

  ```
  hexo d
  ```

## hexo常用命令

| 命令                | 解释                                           |
| ------------------- | ---------------------------------------------- |
| hexo clean          | 清除缓存,会清除public目录下文件,修改配置后使用 |
| hexo g              | 生成静态网页，将md文件转成html, 放到public目录 |
| hexo s              | 启动服务器，可在本地通过localhost:4000预览     |
| hexo d              | 部署到github上                                 |
| hexo d -g           | 生成并部署                                     |
| hexo new "postName" | 新建文章                                       |
| hexo new "pageName" | 新建页面                                       |

