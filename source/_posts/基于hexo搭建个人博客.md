title: 基于hexo搭建个人博客
categories: 学习笔记
tags: 学习笔记
date: 2020-07-26 17:23:00
---
#### 环境依赖
&emsp;&emsp;本文记录下个人博客搭建的过程，是在mac系统搭建的博客，而且强依赖于github。默认需要以下条件都准备好了。搭建完成后的效果可以参考[这里](https://linhao0204.github.io/)。
- brew，mac系统的安装包管理，具体安装可以参考网上教程
- github账号，而且熟悉基本的git操作
<!--more-->

#### 新建git仓库
&emsp;&emsp;新建一个repository，并且名字需要为\*.github.io，其中*为github的用户名。比如我的用户名是linhao0204，所以repository为linhao0204.github.io，而且必须为public。  
&emsp;&emsp;新建完成后，在本地安装git，然后将仓库clone到本地。
```
brew install git
git clone 你的仓库地址
```
#### hexo
&emsp;&emsp;我们博客的搭建依赖于hexo，hexo是一个博客框架，我们只要在特定的文件夹下编写我们的博客就可以了，bexo可以帮助我们编译并上传部署到服务器上，我们这里采用的是github.io当做我们博客的服务器，hexo的具体介绍可以见[这里](https://hexo.io/zh-cn/docs/)。
##### 安装Node.js
&emsp;&emsp;hexo是基于node.js的，所以需要安装node.js
```
brew install node
npm -v  # 检查是否安装完成
```
##### 安装hexo
```
npm install -g hexo-cli
hexo init linhao0204.github.io  # 初始化hexo博客
hexo g  # 编译生成博客
hexo s  # 在本地预览博客
```
&emsp;&emsp;在浏览器上输入`http://localhost:4000/`就可以在本地访问我们自己搭建的博客了。
##### 部署到git
&emsp;&emsp;上面是部署在本地，但是我们要将自己的博客部署到github的服务器上，和我们前面创建的git仓库关联起来。在根目录的站点配置文件`_config.yml`中搜索`deploy`加入以下配置。
```
deploy:
  type: git
  repo: git@github.com:linhao0204/linhao0204.github.io.git  # 这里要换成上面创建的git仓库
  branch: master
```
&emsp;&emsp;同时需要在当前博客目录下安装部署插件。
```
npm install hexo-deployer-git --save  # 安装部署插件
```
&emsp;&emsp;然后在当前博客目录下执行`deploy d`就可以部署到git服务器上了，在`https://你的git用户名.github.io/`就可以访问了。  
&emsp;&emsp;到了这里的话博客的框架就搭建完成，有了个雏形了，下面根据我们个人需求进行修改。
#### 个人优化
##### 修改站点信息
&emsp;&emsp;上面完成的博客信息都是默认的，通过修改`_config.yml`配置文件可以更新为自己的信息，我自己修改的字段如下。
```
# Site
title: Linhao  # 博客名称
subtitle: ''
description: '平时的一些记录和笔记'  # 个人描述
keywords:
author: Linhao
language: zh-CN  # 这字段可以选择博客显示为中文或者英文
timezone: ''
```
##### 修改主题
&emsp;&emsp;hexo可以根据自己喜好选择不同的博客主题，我选择的是[next主题](https://github.com/theme-next/hexo-theme-next)，将该主题clone下来到`theme/next`之后，需要修改站点配置文件`_config.yml`，将配置文件中的`theme:  landscape`改为`theme: next`。然后重新部署后就可以生效。  
&emsp;&emsp;前面一直说到站点配置文件，在`theme/next/`中也有配置文件`_config.yml`，称之为主题配置文件，这里将主题配置文件里将用到的一些配置进行说明。
###### 修改主题排版
&emsp;&emsp;next主题有多个排版，通过修改`Schemes`可以选择对应排版，我选择的是`Pisces`。
```
# Schemes
# scheme: Muse
#scheme: Mist
scheme: Pisces
#scheme: Gemini
```
###### 菜单栏显示
&emsp;&emsp;通过修改`menu`中的配置，可以在菜单栏里添加自己需要的模块，添加完成后需要通过`hexo new page "categories"`来生成对应的主页，在`source/`目录下会生成对应目录`categories`。然年通过菜单栏点击进去后会进入对应的网页。这里需要注意的是需要在生成的`source/categories/index.md`文件中添加`type: "categories"`，否则添加的类别将不会自动更新。`tags`目录也是类似的。
```
menu:
  home: / || fa fa-home
  #about: /about/ || fa fa-user
  # tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
```
###### 修改头像
&emsp;&emsp;头像存放在`/themes/next/source/images/`目录下，将自己的头像放到该目录下之后，修改配置文件中的以下字段即可。
```
avatar:
  # Replace the default image and set the url here.
  url: /images/avatar.jpeg  # 换为自己的头像文件名
```
###### 增加社交账号
&emsp;&emsp;在配置文件中搜索`Social Links`，选择对应的选项之后即可。
###### 搜索和增加文章概况
&emsp;&emsp;如果博客中文章过多的话，next支持本地搜索，通过安装`npm install hexo-generator-searchdb --save`，然后在配置文件中修改`local_search`为`True`，最后需要在站点配置文件`_config.yml`（注意不是主题配置文件，这部分修改的默认都在主题配置里，这里是站点配置文件）添加以下配置。重启后就可以生效了，有的生效时间会比较久，需要耐心等待。
```
# 搜索页面
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```
&emsp;&emsp;next也支持统计每篇博文的字数，并显示大约阅读时长，安装`npm install hexo-wordcount --save`和`npm install hexo-symbols-count-time --save`,然后在配置文件中将`symbols_count_time`设置为`True`，最后也需要在站点配置文件里添加以下配置。
```
# 统计文章时间
symbols_count_time:
  symbols: true
  time: true
  total_symbols: true
  total_time: true
```
###### 博文预览 
&emsp;&emsp;next主题默认在首页会把博文全文都展示出来，这样在博文比较多的时候会比较乱，所以我们可以开启预览的功能，就是在首页只显示博文的部分内容，这个可以通过在博文中加入`<!--more-->`，这样首页只会显示之前的部分内容。
##### 开启gitalk评论
&emsp;&emsp;如果需要在博文中支持评论，next支持各种方式实现，我选用的是gitalk，这个需要我们在github上申请一个`OAuth Apps`和新建一个仓库用于保存评论。
###### 申请Apps
&emsp;&emsp;在自己的github主页，通过右上角的`setting-Developer settings-Developer settings`即可申请，可以按照以下填写信息。
```
Application name: # 可以随意填写
Homepage URL: # 博客主页，比如我的是https://linhao0204.github.io/
Authorization callback URL: # 填的也是https://linhao0204.github.io/
```
&emsp;&emsp;申请之后生成一个`Client ID`和`Client Secret`，后面在修改配置文件的时候会用到这两个。
###### 申请仓库
&emsp;&emsp;和前面申请仓库的流程是一致的，可以随意命名，比如我的是`blog-comment`。
###### 修改配置
&emsp;&emsp;按照以下修改主题配置文件，重新部署后，登录github账号后即可生效。
```
# 选择支持评论的方式，这里采用gitalk
comments:
  # Available values: tabs | buttons
  style: tabs
  # Choose a comment system to be displayed by default.
  # Available values: changyan | disqus | disqusjs | gitalk | livere | valine
  active: gitalk
```
```
# Gitalk
# For more information: https://gitalk.github.io, https://github.com/gitalk/gitalk
gitalk:
  enable: true
  github_id: linhao0204 # 个人的github用户名
  id: location.pathname
  repo: blog-comment # 这是上面新建的仓库名
  client_id:  # 申请Apps时获取到的id
  client_secret:  # 申请Apps时获取到的密码
  admin_user: ["linhao0204"] # 这里需要增加管理员用户，可以初始化issue，否则会提示”未找到相关的 Issues 进行评论“
  distraction_free_mode: true # Facebook-like distraction free mode
  # Gitalk's display language depends on user's browser or system environment
  # If you want everyone visiting your site to see a uniform language, you can set a force language value
  # Available values: en | es-ES | fr | ru | zh-CN | zh-TW
  language: zh-CN
```
##### 支持mathjax
&emsp;&emsp;如果博文中需要支持公式编辑的话，需要在站点配置文件中开启支持mathjax，同时也需要在博文头部添加`mathjax: true`。
```
math:
  per_page: false  # 由于公式显示会比较耗时，只在有需要的博文中才使用，所以这里置为false
  mathjax:
    enable: true
    # See: https://mhchem.github.io/MathJax-mhchem/
    mhchem: true
```
##### 博文编辑器
&emsp;&emsp;在编辑markdown文件中，比较痛苦的就是图片和预览。往markdown中添加图片，picgo可以实现复制，然后上传直接获取图片链接，最后可以直接将链接拷贝到文档中，算是比较方便的，但是需要一个合适的图床。网上有很多图床教程，别人推荐比较多的是采用七牛云，但是得实名注册，也有使用github作为图床的，但是最近好像不能使用了。所以我还是将图片放在github的项目下，图片也不会有很多。  &emsp;&emsp;hexo提供了一个网页插件，通过`npm install hexo-admin --save`安装之后就可以在`http://localhost:4000/admin`上直接编辑博文了。这个插件的好处就是编辑markdown的时候，可以直接将图片拷贝到文章里，然后会直接将图片保存到`source/images/`下，非常方便。
#### 其他
##### 项目下的.gitignore文件
&emsp;&emsp;项目下有部分文件不是必须的，可以不用提交到git上，比如`node_modules`文件夹下的东西只是用于本地编译。
```
# 忽略一些本地编译的东西
public/
db.json
.idea/
.deploy_git/
node_modules/
package.json
package-lock.json

# mac系统的东西
*.DS_Store
```
##### sublime支持markdown实时预览和mathjax
&emsp;&emsp;因为我刚开始打算采用sublime编辑器作为md编辑器，所以也看了下相关配置，这里记录一下。
###### 支持markdown实时预览
- 打开sublime，通过组合键`Ctrl+Shift+P`调出命令面板
- 输入`Package Control: Install Package`，回车
- 分别输入`MarkdownEditing`，`MarkdownPreview`，`LiveReload`后回车即可安装

&emsp;&emsp;`MarkdownEditing`是md文本编辑插件。`MarkdownPreview`支持网页预览，但是不会实时自动更新，通过在`Preferences -> Key Bindings`添加以下语句后，通过快捷键`"command+m"`就可以进行预览。
```
[
	{ "keys": ["command+m"], "command": "markdown_preview", "args": {"target": "browser", "parser":"markdown"}  }
]
```
&emsp;&emsp;为了实现实时预览，就需要安装`LiveReload`了。安装完成后需要检查`Preferences -> Package Settings -> Markdown Preview -> setting`配置文件中的`enable_autoreload`是否为`True`。同时需要通过`Preferences -> Package Settings -> LiveReload -> Plugins -> Enable/disable plugins`将实时预览功能开启。这样设置之后，每次保存之后网页就可以实时预览了。
###### 支持mathjax
&emsp;&emsp;在预览的时候，公式无法正常显示，需要在`Markdown Preview`的`user setting`中添加以下配置。
<details>
  <summary>展开</summary>
  
```  
{
    "enable_mathjax": true,
    "js": [
    	"https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js",
        "res://MarkdownPreview/js/math_config.js"
    ],
    "markdown_extensions": [
        // Python Markdown Extra with SuperFences.
        // You can't include "extra" and "superfences"
        // as "fenced_code" can not be included with "superfences",
        // so we include the pieces separately.
        "markdown.extensions.footnotes",
        "markdown.extensions.attr_list",
        "markdown.extensions.def_list",
        "markdown.extensions.tables",
        "markdown.extensions.abbr",
        "pymdownx.betterem",
        {
            "markdown.extensions.codehilite": {
                "guess_lang": false
            }
        },
        // Extra's Markdown parsing in raw HTML cannot be
        // included by itself, but "pymdownx" exposes it so we can.
        "pymdownx.extrarawhtml",

        // More default Python Markdown extensions
        {
            "markdown.extensions.toc":
            {
                "permalink": "\ue157"
            }
        },
        "markdown.extensions.meta",
        "markdown.extensions.sane_lists",
        "markdown.extensions.smarty",
        "markdown.extensions.wikilinks",
        "markdown.extensions.admonition",

        // PyMdown extensions that help give a GitHub-ish feel
        {
            "pymdownx.superfences": { // Nested fences and UML support
                "custom_fences": [
                    {
                        "name": "flow",
                        "class": "uml-flowchart",
                        "format": {"!!python/name": "pymdownx.superfences.fence_code_format"}
                    },
                    {
                        "name": "sequence",
                        "class": "uml-sequence-diagram",
                        "format": {"!!python/name": "pymdownx.superfences.fence_code_format"}
                    }
                ]
            }
        },
        {
            "pymdownx.magiclink": {   // Auto linkify URLs and email addresses
                "repo_url_shortener": true,
                "repo_url_shorthand": true
            }
        },
        "pymdownx.tasklist",     // Task lists
        {
            "pymdownx.tilde": {  // Provide ~~delete~~
                "subscript": false
            }
        },
        {
            "pymdownx.emoji": {  // Provide GitHub's emojis
                "emoji_index": {"!!python/name": "pymdownx.emoji.gemoji"},
                "emoji_generator": {"!!python/name": "pymdownx.emoji.to_png"},
                "alt": "short",
                "options": {
                    "attributes": {
                        "align": "absmiddle",
                        "height": "20px",
                        "width": "20px"
                    },
                    "image_path": "https://assets-cdn.github.com/images/icons/emoji/unicode/",
                    "non_standard_image_path": "https://assets-cdn.github.com/images/icons/emoji/"
                }
            }
        },
        {
            "pymdownx.arithmatex": { 
                "generic": true 
            }
        }
    ],
}
```
</details>

