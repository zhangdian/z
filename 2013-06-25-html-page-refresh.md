# 2013-06-25-html-page-refresh


##JSP页面刷新

下面总结JSP页面中的三种刷新方法：

###在html中设置

在

	<title>bd17kaka</title>

之後加入下面这一行即可：

	<META HTTP-EQUIV="Refresh" content="10">

意思是每10秒钟刷新一次页面。

###在JSP页面中设置

加入

	<% response.setHeader("refresh","1"); %>

即可，意思是每一秒钟刷新一次。


也可以这样，使页面跳转到另外一个页面：


	<%  
	    response.setHeader("refresh","30;URL=http://www.sina.com");  
	%>

###使用JS

加入如下JS代码：

	<script language="javascript">
		setTimeout("self.location.reload();",1000);
	<script>

意思是每一秒钟刷新一次。
  
