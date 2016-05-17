# 2013-05-25-js-cross-domain

####什么是跨域
在JavaScript中，有一个很重要的安全性限制，被称为“Same-Origin Policy”（同源策略）。这一策略对于JavaScript代码能够访问的页面内容做了很重要的限制，即JavaScript只能访问与包含它的文档在同一域下的内容。

所谓同源是指，__域名，协议，端口__相同。

<!-- more -->

####JSONP解决跨域的问题

JSON(JavaScript Object Notation), JSONP(JSON with Padding)

在同源策略下，在某个服务器下的页面是无法获取到该服务器以外的数据的。

__但img、iframe、script等标签是个例外，这些标签可以通过src属性请求到其他服务器上的数据。利用script标签的开放策略，我们可以实现跨域请求数据，当然，也需要服务端的配合。__

当我们正常地请求一个JSON数据的时候，服务端返回的是一串JSON类型的数据，而我们使用JSONP模式来请求数据的时候，服务端返回的是一段可执行的JavaScript代码。

#####请求JSON数据
URL 	http://lifeblog.bd17kaka.net/json?id=123

返回数据	{"id": 123, "name" : "json"}

#####请求JSONP数据
URL 	http://lifeblog.bd17kaka.net/json?id=123&callback=cb

返回数据	cb({"id": 123, "name" : "json"}); 

上述返回数据包含一段可以执行的代码，客户端应该有cb这个函数。当然，更好返回值写法应该是：
try{cb({"id": 123, "name" : "json"});}catch(e){}

####解决方案

动态创建一个script标签，src地址是要访问的地址：

```
<script type="text/javascript" src="http://lifeblog.bd17kaka.net/json?id=123&callback=cb"></script>
```

服务器接收到请求，返回如下格式的字符串，包含一段可执行的JS代码：

```
try{cb({"id": 123, "name" : "json"});}catch(e){}
```

客户端创建一个cb函数，接收参数，进行相应的处理。

```
function cb(data) {alert(data);}
```

####JQUERY解决方案

#####在AJAX中，将dataType设置为“jsonp”

```
$.ajax({
        dataType: 'jsonp',
        url: 'http://lifeblog.bd17kaka.net/json?id=123',
        success: function(data){
			
        }
});
```

#####使用getJson方法，在地址中加上callback参数，客户端需要有这个callback的实现

```
$.getJSON('http://lifeblog.bd17kaka.net/json?id=123&callback=cb', function(data){
	cb(data);
});
```

#####使用getScript方法

```
function foo(data){
        //处理data数据
}
$.getScript('http://lifeblog.bd17kaka.net/json?id=123&callback=cb');
```

####参考

* [jQuery中利用JSONP解决AJAX跨域问题](http://www.clanfei.com/2012/08/1637.html)
* [深入浅出JSONP--解决ajax跨域问题](http://www.cnblogs.com/chopper/archive/2012/03/24/2403945.html)