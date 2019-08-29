---
layout: post
title:  "Linux下安装Jekyll本地环境 "
date:   2019-08-29
excerpt: "Personal Blog"
tag:
- Jekyll
- Linux

---



<center><H2><b> Linux下安装Jekyll本地环境 </b></H2></center><br>

```
本篇内容操作基于Ubuntu 18.4。在Linux下安装Jekyll本地环境并且配置Jekyll使之能运行某个已有的Jekyll主题博客。（从GitHub上fork的Jekyll主题中需要不同的依赖，重点是调整依赖使之能让当前已有的主题配置成功，并进行本地运行)
``` 
## 1.安装ruby & gem
#### 查看是否有ruby环境， 如果输出版本则说明已安装，可跳过这一步
> ruby-v
#### 安装ruby
> apt-get install ruby
通过`ruby -v`查看是否安装成功

#### ruby安装时会自带gem，通过`gem -v` 进行查看是否存在gem

#### 切换gem的镜像源
> gem sources --remove https://rubygems.org/
> gem sources -a https://gems.ruby-chian.org/
通过`gem source`进行查看源是否切换成功

#### 安装bundle
> gem install bundle

#### 安装jekyll
> gem install jekyll

#### 启动博客
> jekyll new newBlog
> cd newBlog
> jekyll serve

#### 启动已有的博客
如果上述启动不成功，也可以通过同样的方法进行调整。主要的问题在于，安装的bundle或者bundle下的依赖与已有博客配置的GEMFIEL、GEMFILE.loc不同。

通过如下命令，替换bundle中的依赖
> bundle install dependences -v denpendencesversion
> bundle uninstall dependences -v denpendencesversion

最后尝试启动
> bundle exec jekyll server

到此搭建的jekyll blog已经能够通过浏览器访问了，默认入口为：127.0.0.1:4000/



