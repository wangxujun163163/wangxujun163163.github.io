---
title: linux环境hexo和GithubPages搭建个人博客
categories: Hexo
date: 2017-02-14 14:23:02
tags:
 - Hexo
 - Nodejs
---
# 什么是Hexo
 Hexo 是一个简单地、轻量地、基于Node的一个静态博客框架，可以方便的生成静态网页托管在github和Heroku上，引用Hexo作者 @tommy351 的话：

快速、简单且功能强大的 Node.js 博客框架。A fast, simple & powerful blog framework, powered by Node.js.

# 什么是GitHub Pages

GitHub Pages 可以被认为是用户编写的、托管在github上的静态网页。由于它的空间免费稳定， 可以用于介绍托管在github上的Project或者搭建网站。有两种形式: Project Site 和 User/Org Site，二者之间的差异可以戳 GitHub Pages 。

# 配置环境

## 安装 epel源

```
rpm -Uvh http://dl.fedoraproject.org/pub/epel/beta/7/x86_64/epel-release-7-0.2.noarch.rpm
```

## 安装nodejs
```
curl -sL https://rpm.nodesource.com/setup | bash -
yum install -y nodejs
```
## 安装HEXO

```
npm install -g hexo
```
## 初始化
创建一个文件夹，如：Blog，cd到Blog里执行hexo init的。命令：

```
hexo init
```
生成静态页面

```
hexo generate（hexo g也可以）
```
启动本地服务，进行文章预览调试，命令：

```
hexo server
```
浏览器输入http://10.10.3.61:4000/ (因为我的linux  装在虚拟机上 ,如果你的linux是实体机,直接访问 http://localhost:4000),本地已经简单的设置好了，但是现在域名和服务器都是基于自己的电脑，接下来需要跟github进行关联.

## 建立Repository
建立与你用户名对应的仓库，仓库名必须为【your_user_name.github.io】，固定写法 然后建立关联，目录结构如下：


```
.
├── .deploy       #需要部署的文件
├── node_modules  #Hexo插件
├── public        #生成的静态网页文件
├── scaffolds     #模板
├── source        #博客正文和其他源文件, 404 favicon CNAME 等都应该放在这里
|   ├── _drafts   #草稿
|   └── _posts    #文章
├── themes        #主题
├── _config.yml   #全局配置文件
└── package.json

```
## 编辑配置文件_config.yml
翻到最下面，改成我这样子的，注意： : 后面要有空格

```
deploy:
  type: git
  repository: https://github.com/wangxujun163163/wangxujun163163.github.io.git
  branch: master
```
执行如下命令才能使用git部署

```
npm install hexo-deployer-git --save
```
## 发布博客到github

```
hexo deploy
```
现在个人博客就建立好了
然后再浏览器中输入https://wangxujun163163.github.io/就行了，我的 github 的账户叫 wangxujun163163 ,把这个改成你 github 的账户名就行了

## 添加博文

```
hexo new "postName"  #新建博文,其中postName是博文题目
```
博文会自动生成在博客目录下source/_posts\postName.md

文件自动生成格式:

```
title: "first article"  #博文题目
date: 2014-11-21 11:25:38      #生成时间
tags:                    #标签, 多个标签也可以使用格式[tag1, tag2, tag3,...]
- tag1
- tag2
- tag3

---
正文, 使用 Markdown 语法书写
```
如果不想博文在首页全部显示, 并能出现阅读全文按钮效果, 需要在你想在首页显示的部分下添加

```
<!--more-->
```
## 主题更改

```
git clone git@github.com:litten/hexo-theme-yilia.git themes/yilia
```
在./_config.yml，修改主题为yilia
theme: yilia
这是yilia主题的预览效果
![image](http://note.youdao.com/yws/public/resource/81f0f25c06b0bb3c1894a75b072288c0/xmlnote/6B65D3F089EE4224912395E34B562A2D/12922)

如果觉得不好看可以换next主题

```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

在./_config.yml，修改主题为next
theme: next
在切换主题之后 我们最好使用 hexo clean 来清除 Hexo 的缓存。
## 验证主题

首先启动 Hexo 本地站点，并开启调试模式（即加上 --debug），整个命令是 hexo s --debug。 在服务启动的过程，注意观察命令行输出是否有任何异常信息
当命令行输出中提示出：

```
INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
```

此时即可使用浏览器访问 http://localhost:4000，检查站点是否正确运行。



## 设置 站点语言


在./_config.yml，设置简体中文
language: zh-Hans

## 设置 头像
编辑 站点配置文件_config.yml， 新增字段 avatar， 值设置成头像的链接地址。
avatar: https://avatars3.githubusercontent.com/u/3031198?v=3&s=460

## 添加多说评论
使用多说前需要先在 多说 创建一个站点，填写站点相关信息。 多说域名 这一栏填写的即是你的 duoshuo_shortname
编辑 站点配置文件_config.yml， 新增字段 duoshuo_shortname
duoshuo_shortname: wangxujun163163

## 添加百度统计
登录 百度统计， 定位到站点的代码获取页面

```
<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "https://hm.baidu.com/hm.js?54d6bb2751acd80aacb143d98697dce2";
  var s = document.getElementsByTagName("script")[0]; 
  s.parentNode.insertBefore(hm, s);
})();
</script>
```
编辑 站点配置文件， 新增字段 baidu_analytics 字段，值设置成你的百度统计脚本 id

## 发布静态代码到github

```
hexo clean  #删除缓存
hexo g #生成静态页
hexo d  #发布代码
```
next 主题的详细配置请参考 [http://theme-next.iissnan.com/theme-settings.html](http://theme-next.iissnan.com/theme-settings.html/)

## 开启打赏功能
 只需要 主题配置文件 中填入 微信 和 支付宝 收款二维码图片地址 即可开启该功能。
reward_comment: 坚持原创技术分享，您的支持将鼓励我继续创作！
 wechatpay: /path/to/wechat-reward-image
 alipay: /path/to/alipay-reward-image
 
##  腾讯公益404页面

腾讯公益404页面，寻找丢失儿童，让大家一起关注此项公益事业！效果如下 https://wangxujun163163.github.io/404.html

使用方法，新建 404.html 页面，放到主题的 source 目录下，内容如下：


```
<!DOCTYPE HTML>
<html>
<head>
  <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="robots" content="all" />
  <meta name="robots" content="index,follow"/>
</head>
<body>

<script type="text/javascript" src="http://www.qq.com/404/search_children.js"
        charset="utf-8" homePageUrl="your site url "
        homePageName="回到我的主页">
</script>

</body>
</html>
```
## 添加「标签」页面


```
hexo new page tags
```

注意：如果有启用 多说 或者 Disqus 评论，页面也会带有评论。 若需要关闭的话，请添加字段 comments 并将值设置为 false，如：

```
---
title: tags
date: 2017-02-14 17:38:06
type: "tags"
comments: false
---
```


## 添加「分类」页面

```
hexo new page categories
---
title: categories
date: 2017-02-14 17:39:40
type: "categories"
comments: false
---
```

## 订阅微信公众号
在每篇文章的末尾显示微信公众号二维码，扫一扫，轻松订阅博客。

在微信公众号平台下载您的二维码，并将它存放于博客source/uploads/目录下。

然后编辑 主题配置文件，如下：

配置示例

```
# Wechat Subscriber
wechat_subscriber:
  enabled: true
  qcode: /uploads/wechat-qcode.jpg
  description: 欢迎您扫一扫上面的微信公众号，订阅我的博客！
```




``

<!--more-->
