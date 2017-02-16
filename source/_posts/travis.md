---
title: 手把手教你使用Travis CI自动部署你的Hexo博客到Github上
categories: 利用Travis
date: 2017-02-16 16:24:03
tags:
 - 利用Travis
 - Hexo
 - Nodejs
---
# 前言
前面一篇文章介绍了在github上使用hexo搭建博客的基本环境和hexo相关参数设置等,博客基本上是可以完美运行了。但是，有一点是不太好，就是源码同步问题，如果在不同的电脑上写文章会非常不方便,必须借助U盘,因为我的linux环境是配在虚拟机里面的 ,要把虚拟机文件装在U盘里面,这样才能解决家里和公司写博客的麻烦,后来搜索发现大家都用Travis CI 自动部署博客,简单说就是把网站源码放在dev分支,hexo生成的静态文件还是保留在master分支,这个没办法改变 ,本地增加.travis.yml文件,只要有了这个文件,你每次push代码Travis CI都能帮你从dev分支取出代码部署到master分支去，代码部署后的效果请点击，https://github.com/wangxujun163163/wangxujun163163.github.io/

# Travis CI 工作原理 
简单来说，Travis CI原理就是当你每次提交commit到在github后，它会自动检测你的提交，同时根据的配置文件，生成一个Linux虚拟机来运行你的命令，通常这些命令用于测试，构建等。在我们的要求下，就可以用它运行一些hexo g d之类的命令来自动生成、部署我静态网页。

# 设置Personal Access Token
Personal Access Token  是github API访问的token ,利用这个token 可以让Travis CI check 你的代码 ，push 你的代码.
新建Personal Access Token,点击右上角头像旁边的三角，在菜单中点击“Setting”，进入设置页面
![image](http://note.youdao.com/yws/public/resource/13bbafbfe22dbf6d1efb14e01a8de016/xmlnote/530611D1971E4ABD82C8B4463171BEEA/13253)
点击左侧栏的最下面的“Personal access tokens”，创建Personal access tokens,点击右上角的“Generate new token”，输入用户密码，进入“New personal access token”页面,
![image](http://note.youdao.com/yws/public/resource/13bbafbfe22dbf6d1efb14e01a8de016/xmlnote/473DA3654D114E14A31526D15FB5B064/13248)
设置Token description（其实就是名称），选择相应的权限，如下图所示,
![image](http://note.youdao.com/yws/public/resource/13bbafbfe22dbf6d1efb14e01a8de016/xmlnote/74248C75B748423EAEAA368A902D331F/13251)

点击“Generate token”按钮，生成Personal access tokens。如下图中红线标注的部分。注意：这行token只会在刚刚创建完成后显示一次，以后不再显示。因此，复制并保存到本地。 


# 配置Travis CI

登录并配置Travis CI(https://travis-ci.org/)将鼠标放在用户名上，在弹出的菜单中点击“Accounts”，将会显示你在GitHub上的仓库。 找到自己的博客项目，点击X号，将其变成√号。再点击右侧的齿轮，进入该仓库的配置页面。 在项目的设置中开启Build only if .travis.yml is present这一项,
![image](http://note.youdao.com/yws/public/resource/13bbafbfe22dbf6d1efb14e01a8de016/xmlnote/FB9DFB1B5CC0498F81DBC148E8435ABF/13254)


# 本地安装Travis
```
#删除旧版ruby
yum remove ruby ruby-devel -y
wget  http://cache.ruby-lang.org/pub/ruby/ruby-2.4-stable.tar.bz2
tar xvf  ruby-2.4-stable.tar.bz2 
cd ruby-2.4.0/
yum install  openssl-devel
./configure --prefix=/usr/local/ruby --with-opessl
make && make install

# 安装travis
gem isntall travis
```
# 新建配置文件
首先打开博客项目文件夹，在项目根目录新建.travis.yml配置文件。

```
$ cd /root/blog
$ touch .travis.yml
```
# 登录travis

```
travis login --auto
```
然后会提示输入github的用户名和密码

# 新建变量
在项目根目录下执行:

```
# 这里的 REPO_TOKEN 是变量名,在后面的配置文件中会用到
# TOKEN 是上面github生成的Token
travis encrypt 'REPO_TOKEN=<TOKEN>' --add
```
此操作会在.travis.yml文件中添加下面的信息

```
env:
global:
  secure: fxBE17yzFhC2+FjwVLYbgIhggyfliv3dFCDozTJD7U3n...
```
# Travis配置文件


```
vim  .travis.yml

language: node_js
node_js:
  - "4"  # nodejs的版本
branches:
  only:
  - dev  # 设置自动化部署的源码分支
env:
  global:
    secure: oQSAOgn7y+FUjwpudsPYKKpxOAiIbztL4RB80vZjX05X0c52+S1nsfxtC6nkvNHfhr4WsX/qY6CE+h0rFyYvMXXjVsGgq9HH4wYgi7xPl7w+b+HQo6bc8tu3JHrx+cML9prfz2BwqjaCmKlMff7ciif+Gf75fgLi9g0o+pF8gWATwi4wfb4YB+zIRU/lisbLt+M2FZp9+rY/B0Bl1fMuBZv6+J53b3BJJ3ZvCS7ifLcnXBowKqpk+ubRJUcsYT0qRjE7/++9ipKpiQvTt1ItkDKDAqxe+ISMbBL4UwNIJD/PpqEIrxFpljEn/BRtn4XX3QI3RWnqxlDBv/ojHzvI4UTz83rR+iYgTAqaPokByV+1m+BQtTtjbTYnCGBpLtFDXec8NvAY0DD64cCXhhjouRPSinEqEMOxxcQ6f5OYhEG1zQ3l1paLyBKGpcjuydD0tlefcXUO/6Kb8zQRLFadeJ1VfWAZVfOX68I9o4SgE/d8TPZi/d8XaHc1646eWb8UVr4GUz2oj1tot8+NI+3J4IwTT5okXLAQpcWqveNin5+yegWeef6aBydoy7NtA/yZYdAgRbPJUa7Mq5fwO498Wx1vY8lJpKCsJlwFYqj1sX365PsWcHiVB9K+KMoZFhlk3BXpPPeHgpxR6vt6QyrouD23tU6ZBJXjuRDT4INSjEQ=
    
before_install:
- export TZ='Asia/Shanghai'  # 设置时区
- npm install -g hexo
- npm install -g hexo-cli
- npm install hexo-server
before_script:
# 设置github账户信息
- git config --global user.name "wangxujun163163" #设置github用户名
- git config --global user.email wangxujun163163@163.com #设置github用户邮箱
# github仓库操作
- sed -i'' "s~git@github.com:wangxujun163163/wangxujun163163.github.io.git~https://${REPO_TOKEN}:x-oauth-basic@github.com/wangxujun163163/wangxujun163163.github.io.git~" _config.yml
# 安装依赖组件
install:
- npm install
# 执行的命令
script:
- hexo clean
- hexo generate
# 执行的成功后执行 
after_success:
- hexo deploy
```

# 上传网站源码到github dev分支
新建仓库blog，将原来博客目录下的以下内容复制到blog文件夹中

```
mkdir  github
cd github/
git init blog
cd blog
cp  /root/blog/*   .  -r
```
修改.gitignore文件，取消对node_modules文件夹的忽略。（即删除对应的行）

```
# 将github仓库改为自己的博客仓库
git remote add origin git@github.com:wangxujun163163/wangxujun163163.github.io.git
# 添加文件
git add .
# 提交修改
$ git commit -m "test travis"
# 推送至远程仓库dev分支
[root@localhost blog]# git push -u origin dev
error: src refspec dev does not match any.
error: 无法推送一些引用到 'git@github.com:wangxujun163163/wangxujun163163.github.io.git'

[root@localhost blog]# git branch  -a  #查看本地仓库的分支
* master
[root@localhost blog]# git push origin master:dev

git  commit  -m   "modify nodejs version"   .travis.yml 
[root@localhost blog]# git push origin master:dev
```
我push代码的时候发生了错误,忘记指定本地的分支了 

push本地的代码至远程仓库之后，在https://travis-ci.org后台查看相关情况。
![image](http://note.youdao.com/yws/public/resource/13bbafbfe22dbf6d1efb14e01a8de016/xmlnote/F45D3C1BB688449CB2C1DBF15290170C/13323)




