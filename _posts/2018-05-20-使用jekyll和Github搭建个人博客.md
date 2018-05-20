---
layout: post
title: 使用jekyll和Github搭建个人博客
category: life
---
很多人都会写一些文章发布到博客上，有的人会自己买域名和空间搭建独立博客，更多的人会选择CSDN或者博客园等免费空间，
在此给大家介绍一种简单免费的方式来搭建个人博客。

## 使用jekyll和Github三步搭建个人博客
1. 在 Github 上建一个库，库的名字是xxx.github.com，其中的xxx是你的github的账号名.

2. 在Jekyll模板中选择自己喜欢的模板clone到本地（这里选Bef做实例).

3. 对模板中的信息进行修改(注释掉_config.yml文件里：baseurl：“/bef”)，将模板push到自己的库中，然后访问xxx.github.io，可访问到自己的博客，so easy
注:由于此模板图片较多，所以加载较慢，视觉效果还是不错的。

## So cool发生了什么？
为什么只是简单的三步操作，就完成了个人博客的搭建呢？
整体思路:先在本地编写符合Jekyll规范的网站源码(在模板基础上更改)，然后上传到github，这种上传并不是单纯的上传，
而是会经过Jekyll程序的再处理。由github生成并托管整个网站。
### Jekyll是什么？
   Jekyll是一个简单的静态站点生成器，它会根据网页源码生成静态文件。是两大静态博客主流框架（jekyll和hexo）之一。
jekyll有很多模板，可以结合gitHub用来搭建免费的个人博客，jekyll使用Liquid模板语言(官方文档),是基于ruby的，
所以如果在本地使用jekyll必须先搭建jekyll环境，我的建议是不必花时间在本地安装环境，由于gitHub page已经安装好环境，
所以直接push到gitHub看实际效果更为明智.
### Github Pages是什么？
   Github Pages 是Github 面向用户、组织和项目开放的公共静态页面搭建托管服务，允许站内生成网页，
也允许用户自己编写网页，然后上传。站点可以被免费托管在 Github 上，可以选择使用 Github Pages 默认提供的域名 github.io 或者自定义域名来发布站点。Github Pages 支持自动利用 Jekyll 生成站点，Github Pages可以被认为是用户编写的、托管在github上的静态网页。
### 优劣分析
* 优势：简单，免费，文章保存在github上，并且使用git的版本管理功能，不用担心文章遗失，可以在任何时间地点写文章push到gitHub发布，而且支持markdown语法，可以把主要精力放在写文章上。

* 劣势：有一定技术门槛，你必须要懂一点git和网页开发。它生成的是静态网页，添加动态功能必须使用外部服务，例如评论功能。因为没有用到数据库，每运行一次都必须遍历全部的文本文件，网站越大，生成时间越长。所以它不适合大型网站。

* 但是，总的来说，搭建中小型Blog，特别是个人博客，确实是个很好的选择。既拥有绝对管理权，又享受github带来的便利，更主要的是，这一切是免费的，github提供无限流量。

## 刨析实例

1. 目录结构

2. _config.yml:这个文件特别重要是保存配置的，标题，描述，评论等；基本上博客的所有配置可以放在这个文件里。

3. _layouts这里存放的是模板文件，发布的文章会根据文章顶部的yaml文件头来设置一些元数据，如"layout:default"，
表示该文章的模板使用_layouts目录下的post.html文件；"title: "，表示该文章的标题，如果不设置这个值，
默认使用嵌入文件名的标题等等。

4. _includes这里面的就是可以重复利用的文件。这个文件可以被其他的文件包含，重复利用。就是引用head.html。

5. _posts这里的文件就实际的文章内容了。文件名必须使用“年-月-日-文章标题.后缀名”的格式。在博客上发布文章的时候，
只需要在此文件夹中加入带有 YAML 头信息的 markdown 文件，然后 push 到 Github，就会被自动渲染成 HTML。

## 给个人博客加评论功能。
第三方的评论系统有很多，这里选择gitalk来进行演示，gitalk是纯客户端组件，无 Server 端，使用 Github 登录，
所有评论数据存储为 Github Issue。

* 首先需要注册GitHub Application，Authorization callback URL 填写当前使用插件页面的域名。具体见下图。

* 创建comments.html，内容如下，添加到about页面（这里以about页面为例，其它页面类似）
{% highlight css %}
{% if page.comments != false %}
    {% if site.comments_provider == 'gitalk' %}
        <div id="gitalk-container"></div>
        <script src="/assets/js/gitalk.min.js"></script>
        <script>
        var gitalk = new Gitalk({
            id: '{{ page.url }}',
            clientID: '{{ site.gitalk.clientID }}',
            clientSecret: '{{ site.gitalk.clientSecret }}',
            repo: '{{ site.gitalk.repo }}',
            owner: '{{ site.gitalk.owner }}',
            admin: ['{{ site.gitalk.owner }}'],
            labels: ['gitment'],
            perPage: 50,
        })
        gitalk.render('gitalk-container')
        </script>
    {% endif %}
{% endif %}
{% endhighlight %}

* 在gitHub上创建仓库blog-comments，添加如下代码到_config.yml
注：clientID和clientSecret是第一步注册的时候得到的，owner是自己的用户名
```
comments_provider: gitalk
gitalk:
    owner: jueye3
    repo: blog-comments
    clientID: fa5504fe07f319cba9ee
    clientSecret: 30532bea61e8b63dc5a852e448621a8c89cef99b
```

* 下载gitalk.min.js（放到assets/js文件夹下）和gitalk.css（放到assets/css文件夹下）。

* 使用gitHub账号登陆初始化，就可以使用评论功能了。

## 给个人博客加统计功能
相信很多人对自己的博客访问量还是很感兴趣的，好的关注度和大的访问量能给自己写博客很大信心，
下面我就介绍一下如何给自己的博哥添加统计功能（以百度统计为例）

1. 在百度统计上注册账号并登陆，然后新增网站

2. 在_includes下创建baidu-anaylysis.html，内容是百度统计生成的代码。

3. 在head.html文件中添加{% include  baidu-anaylysis.html %}，Push后可以检查是否成功。

4. 代码正确安装，可以查看报告了

## 给个人博客绑定域名

首先需要有域名，如果没有域名，可以通过很多渠道注册域名，有了域名之后，新建一个CNAME文件（无后缀名），然后用文本编辑器打开，
在首行添加你的网站域名，如http://xxxx.com，注意前面没有http://example.com或者xxx.example.com。
在域名解析提供商，下面以百度云为例。

（1）先添加一个CNAME，主机记录写@，后面记录值写上你的http://xxxx.github.io

（2）再添加一个CNAME，主机记录写www，后面记录值也是http://xxxx.github.io

这样别人用www和不用www都能访问你的网站（其实www的方式，会先解析成http://xxxx.github.io，然后根据CNAME再变成http://xxx.com，中间是经过一次转换的）。
注:上面，我们用的是CNAME别名记录，也有人使用A记录，后面的记录值是写github page里面的ip地址，但有时候IP地址会更改，导致最后解析不正确，所以还是推荐用CNAME别名记录要好些，不建议用IP。等十分钟左右，刷新浏览器，用你自己域名访问下试试

## 结束语
按照本篇文章搭建起个人博客应该不成问题，并且应该不会花费多少时间就能完成，但是要博客个性化，搭建一个自己喜欢的博客，
可能就需要下一番功夫了，我建议还是模板上改，先找一个自己喜欢的模板，在改的过程中会有一些自己的想法，
慢慢搭建一个自己喜欢的具有个人风格的博客。欢迎大家和我交流。
