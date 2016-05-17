# 2012-11-29-setup-custom-octopress


###1. Octopress个性化设置文件
主要文件都在source/_includes/文件夹中，其中
```
/asides/ #包含侧边栏的项目
/custom/ #包含个性化头部、导航栏，footer以及侧边栏等
```
/custom中包含的都是个性化的页面，其中也包含
```
/asides/ #包含个性化的侧边栏项目
/*.html  #个性化的页面，可以在这里设置自己个性化的页面
```
那么，设置自己个性化的页面，主要修改的是source/_incledes/中的内容。
此外，修改了文件内容之后，还需要修改_config.yml中的部分内容才能生效，具体见后面的设置。

<!-- more -->

###2. sina相关
sina的开放平台上提供了很多可以作为插件到octopress中的元素，包括分享按钮、关注按钮等等，见链接：

1. [sina开放平台](http://open.weibo.com/)
2. [sina分享按钮界面](http://open.weibo.com/sharebutton)

####2.1 sina分享
要想在边框栏设置sina分享，要修改三个地方的内容：

* 添加文件/source/_includes/custom/asides/sina_weibo_sharing.html文件，其中内容如下：
{% include_code ruby sina_weibo_sharing.html %}
* 修改_config.yml，在最后添加下面的代码：
```
# Weibo 
weibo_uid: 1930318973
weibo_fansline: 0   # How many lines for the fan list
weibo_show: true    # Whether you want your weibo content to be shown
weibo_pic: true     # Whether you want the pictures in weibo to be shown
weibo_skin: 10      # Please refer to http://weibo.com/tool/weiboshow
weibo_share: true   # Whether show the sharing button
```
* 修改_config.yml，在defaule_asides数组中添加一项：
```
custom/asides/sina_weibo_sharing.html
```

####2.2 sina关注
要想在边框栏设置sina关注，要修改两个地方的内容：

* 添加文件/source/_includes/custom/asides/sina_weibo_follow.html文件，其中内容如下：
{% include_code sina_weibo_follow.html %}
* 修改_config.yml，在defaule_asides数组中添加一项：
```
custom/asides/sina_weibo_follow.html
```

####2.3 将新浪分享添加到每个博客页面下方
在_include/post/sharing.html添加如下代码即可：
```
{% if site.weibo_share %}
<script type="text/javascript" charset="utf-8">
(function(){
  var _w = 72 , _h = 16;
  var param = {
    url:'{{ site.url }}{{ page.url }}',
    type:'3',
    count:'1', /**是否显示分享数，1显示(可选)*/
    appkey:'', /**您申请的应用appkey,显示分享来源(可选)*/
    title:'', /**分享的文字内容(可选，默认为所在页面的title)*/
    pic:'', /**分享图片的路径(可选)*/
    ralateUid:'', /**关联用户的UID，分享微博会@该用户(可选)*/
	language:'zh_cn', /**设置语言，zh_cn|zh_tw(可选)*/
    rnd:new Date().valueOf()
  }
  var temp = [];
  for( var p in param ){
    temp.push(p + '=' + encodeURIComponent( param[p] || '' ) )
  }
  document.write('<iframe allowTransparency="true" frameborder="0" scrolling="no" src="http://hits.sinajs.cn/A1/weiboshare.html?' + temp.join('&') + '" width="'+ _w+'" height="'+_h+'"></iframe>')
})()
</script>
  {% endif %}
```

###3. About.html
可以添加关于自己的介绍页面，同样，需要修改两个地方的数据：

* 添加文件/source/_includes/custom/asides/about.html的内容
* 修改_config.yml，在defaule_asides数组中添加一项：
```
custom/asides/about.html
```

###4. 添加评论
Octopress使用的是[disqus](http://www.disqus.com/)插件来展示文章的评论。
####4.1 disqus
在[disqus官网](http://www.disqus.com/)注册一个账号，然后按提示设置shortname，Website name以及Website URL即可。这里的Website必须填写你要使用该插件的网址，也就是你的blog地址，对于我则是http://zhangdian.github.com。这里的shortname是要在octopress设置中填写的值。

####4.2 octopress配置
需要再_config.yml中填写disqus_short_name的值，也就是在注册disqus时的shortname的值，那么这行设置最后的形式就是“disqus_short_name: value”。注意，冒号后面一定要有一个空格，不然会报错。

####4.3 文章设置
在文章头部，有一个选项是“comments”，把他设置成true。

####4.4 使用到的文件
在octopress结构中，实际上是在source/_includes/after_footer.html中引入了评论页面：
```
{{ "{% include disqus.html" }} %}
{{ "{% include facebook_like.html" }} %}
{{ "{% include google_plus_one.html" }} %}
{{ "{% include twitter_sharing.html" }} %}
{{ "{% include custom/after_footer.html" }} %}

```
其中第一个就是。后面三个分别是facebook、google+、twitter的一些东西，最后一项是引入个性化的设置。在disqus.html中：
```
{{ "{% if site.disqus_short_name and page.comments != false" }} %}
<script type="text/javascript">
      var disqus_shortname = '{{ site.disqus_short_name }}';
      {% if page.comments == true %}
        {% comment %} `page.comments` can be only be set to true on pages/posts, so we embed the comments here. {% endcomment %}
        // var disqus_developer = 1;
        var disqus_identifier = '{{ site.url }}{{ page.url }}';
        var disqus_url = '{{ site.url }}{{ page.url }}';
        var disqus_script = 'embed.js';
      {% else %}
        {% comment %} As `page.comments` is empty, we must be on the index page. {% endcomment %}
        var disqus_script = 'count.js';
      {% endif %}
    (function () {
      var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
      dsq.src = 'http://' + disqus_shortname + '.disqus.com/' + disqus_script;
      (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    }());
</script>
{% endif %}
```
只有在全局变量disqus_short_name和页面变量comments同时为true的时候，才会加载评论页面。
####4.5 关于source/_includes/post/sharing.html页面
这个页面里面包含的内容紧跟着文章内容下面显示，而我们上述讨论的评论页面是在这下面显示的。

###5. 遇到的问题
####5.1 undefined method `Py_IsInitialized' for RubyPython::Python:Module
在使用插件功能给博客添加边框栏项目的时候，提示上述错误，原因是没有安装python，解决办法是首先安装python，然后bundle update更新一下，就ok了。参考网站：

1. [Py_IsInitialized error](https://github.com/github/gollum/issues/225)
2. [Exception on generate codeblock with "lang:" on Windows](https://github.com/imathis/octopress/issues/262)