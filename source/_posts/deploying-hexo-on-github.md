---
title: 在 GitHub Pages 上面搭建 Hexo 静态博客
tags:
  - GitHub
  - Hexo
categories:
  - 网站相关
date: 2017-07-08 11:46:31
---
### 前言
本站迁移至 GitHub Pages 已经月余，毕竟是静态页面，访问速度令人满意，不过其搭建和发布还是不同于传统的博客程序，在此记录下搭建过程。

### 介绍
GitHub Pages 本用于介绍托管在 GitHub 的项目，也可以用来搭建博客，有1GB 的免费空间。官方推荐使用 [Jekyll](http://jekyllrb.com/) 来搭建，不过我看上了 Hexo 的主题，所以选了它。

Hexo 是一个基于 Node.js 的快速、简洁且高效的博客框架，使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页并托管在 Github Pages 和 Heroku 等平台上。Hexo 作者是来自台湾的 [tommy351](https://github.com/tommy351)。

<!--more-->

### GitHub Pages 设置
1. 首先你需要注册一个 GitHub 帐号；
2. 新建一个与你用户名对应的仓库，仓库名必须为 **username.github.io**；
3. 在仓库中新建一个分支，比如 hexo；
4. 将该分支设置成缺省分支；

>上面最后两个操作的用意是将 hexo 分支用作 Hexo 博客源码的备份分支，将原来的默认缺省分支 master 作为发布静态内容的目标分支，这样你可以很方便的切换你的写作环境到另外一台电脑。

### Hexo 安装
因为 GitHub Pages 是纯静态托管平台，不能像传统博客程序那样，找个浏览器就可以写，所以需要搭建能发布静态内容的环境，我们这里用 Hexo 来生成静态内容并发布到 GitHub Pages。

#### 安装 Git
我使用的发行版是 Ubuntu 16.04 LTS （Xenial Xerus），所以安装 Git 的命令就很简单 `$ sudo apt-get install git-core`

#### 安装 Node.js
最好的方法是使用 [ Node Version Manager](https://github.com/creationix/nvm)。

##### cURL:
`$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash`

##### Wget:

`$ wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash`

当 nvm 装好以后，关掉 terminal 终端窗口再重新打开，运行以下命令安装 Node.js：

`$ nvm install stable`

#### 安装 Hexo
`$ npm install -g hexo-cli`

如果你的网络访问 npm 源比较慢，你可以使用淘宝的 NPM 镜像（每10分钟与官方源同步一次）来安装：

`npm install -g hexo-cli --registry=https://registry.npm.taobao.org`

#### Hexo 初始化
当 Hexo 安装好以后，运行 hexo init `<folder>` 来初始化。

``` bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```

初始化之后，目录结构看起来应该像下面的样子:

``` plain
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
##### _config.yml

网站的**配置**信息，您可以在此配置大部分的参数。

##### package.json
应用程序的信息。[EJS](http://embeddedjs.com/), [Stylus](http://learnboost.github.io/stylus/) 和 [Markdown](http://daringfireball.net/projects/markdown/) renderer 已默认安装，您可以自由移除。

``` json
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": ""
  },
  "dependencies": {
    "hexo": "^3.0.0",
    "hexo-generator-archive": "^0.1.0",
    "hexo-generator-category": "^0.1.0",
    "hexo-generator-index": "^0.1.0",
    "hexo-generator-tag": "^0.1.0",
    "hexo-renderer-ejs": "^0.1.0",
    "hexo-renderer-stylus": "^0.2.0",
    "hexo-renderer-marked": "^0.2.4",
    "hexo-server": "^0.1.2"
  }
}
```

##### scaffolds

**模版**文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。

Hexo的模板是指在新建的markdown文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。

##### source

资源文件夹是存放用户资源的地方。除 `_posts` 文件夹之外，开头命名为 `_` (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 `public` 文件夹，而其他文件会被拷贝过去。

##### themes

**主题**文件夹。Hexo 会根据主题来生成静态页面。

更多关于 Hexo 建站和配置的详细信息，可以参考[官方文档](https://hexo.io/zh-cn/docs/setup.html)

#### 发布 & 部署
##### 新建文章

``` bash
$ hexo new [layout] <title>
```

新建一篇文章。如果没有设置 `layout` 的话，默认使用 [_config.yml](configuration.html) 中的 `default_layout` 参数代替。如果标题包含空格的话，请使用引号括起来。

##### 生成静态内容

``` bash
$ hexo generate
```
生成静态文件。

选项 | 描述
--- | ---
`-d`, `--deploy` | 文件生成后立即部署网站
`-w`, `--watch` | 监视文件变动

该命令可以简写为
```bash
$ hexo g
```

##### 本地调试

``` bash
$ hexo server
```

启动服务器。默认情况下，访问网址为： `http://localhost:4000/`。

选项 | 描述
--- | ---
`-p`, `--port` | 重设端口
`-s`, `--static` | 只使用静态文件
`-l`, `--log` | 启动日记记录，使用覆盖记录格式

##### 部署
部署到 Github 前需要配置 **_config.yml** 文件，首先找到下面的内容
``` plain
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type:
```

然后将它们修改为
``` plain
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repo:https://github.com/randyzhong/randyzhong.github.io.git
  branch: master
```
另外，还需要安装 git 部署插件：
``` bash
$ npm install hexo-deployer-git --save
```

**部署网站：**
``` bash
$ hexo deploy
```

参数 | 描述
--- | ---
`-g`, `--generate` | 部署之前预先生成静态文件

生成静态文件并发布的命令可以简写为：
```bash
$ hexo -d g
```

### 附加配置
本站使用了 maupassant 主题，并且安装了几个插件，所以 package.json 文件内容相比默认的有变化

``` json
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": "3.3.8"
  },
  "dependencies": {
    "hexo": "^3.2.0",
    "hexo-asset-image": "0.0.3",
    "hexo-deployer-git": "^0.3.1",
    "hexo-generator-archive": "^0.1.4",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-feed": "^1.2.2",
    "hexo-generator-index": "^0.2.0",
    "hexo-generator-sitemap": "^1.2.0",
    "hexo-generator-tag": "^0.2.0",
    "hexo-helper-qrcode": "^1.0.2",
    "hexo-renderer-ejs": "^0.3.1",
    "hexo-renderer-jade": "^0.3.0",
    "hexo-renderer-marked": "^0.3.0",
    "hexo-renderer-sass": "^0.3.2",
    "hexo-renderer-stylus": "^0.3.1",
    "hexo-server": "^0.2.0",
    "hexo-wordcount": "^3.0.2"
  }
}
```

所以在安装了 Hexo 之后，可以运行如下命令添加这些插件：
``` bash
$ npm install hexo-asset-image --registry=https://registry.npm.taobao.org --save
$ npm install hexo-deployer-git --registry=https://registry.npm.taobao.org --save
$ npm install hexo-generator-feed --registry=https://registry.npm.taobao.org --save
$ npm install hexo-generator-sitemap --registry=https://registry.npm.taobao.org --save
$ npm install hexo-renderer-jade --registry=https://registry.npm.taobao.org --save
$ npm install hexo-renderer-stylus --registry=https://registry.npm.taobao.org --save
$ npm install hexo-wordcount --registry=https://registry.npm.taobao.org --save
$ npm install hexo-helper-qrcode --registry=https://registry.npm.taobao.org --save
```

#### CNAME 设置
在发布完成以后，就可以使用 https://yourname.github.io 来访问你的主页了。
如果你不想用 github.io 的二级域名，你可以使用自己的域名。
1. 在Hexo 的 source 目录下创建 CNAME 文件。
2. 填写自己的域名如 winotes.net 或者 www.winotes.net，保存退出。
3. 登录你的域名解析商，比如 DNSPod，先添加域名，然后添加记录，设置如下：

主机记录 | 记录类型 | 线路类型 | 记录值 | MX优先级 | TTL
--- | ---
@ | CNAME | 默认 | yourname.github.io. | - | 10
www | CNAME | 默认 | yourname.github.io. | - | 10

当然，你也可以通过创建DNS A 记录直接指定 GitHub Pages 的 IP 来解析：
* 192.30.252.153
* 192.30.252.154

### 博客源码备份
``` bash
$ git add .
$ git commit -m "comments"
$ git push original hexo
```
以下目录不用上传，所以在 .gitignore 里面排除掉即可：
``` plain
.git/
public/
.deploy_git/
```


