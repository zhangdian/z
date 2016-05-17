# 2012-11-29-octopress-sharing-code-snippets

###1.介绍
octopress文档中提供了很多种的代码分享高亮方法，其中我用的最多的就是两种：

* Backtick Code Blocks
* Include Code Snippets

###2. Backtick Code Blocks
这种方式可以用于代码片段的分享，其格式如下

    ``` [language] [title] [url] [link text]
    code snippet
    ```

其中带‘[]’的都是可选项。
#### 问题:

* 我把可选项都带上，但是不能正确显示，在后台一直提示“favicon.ico无法找到”。

###3. Include Code Snippets
这种方式可以将文件系统中的任何文件导入到博客的任何位置，并且还提供高亮和代码下载。
####3.1 语法

    {{ "{% include_code [title] [lang:language] path/to/file" }} %}

其中title是标题；lang是你的代码的语言，可以提供高亮的功能；

文件路径的默认根目录在_config.yml中的变量code_dir中，而其默认值是source/downloads/code，只需要把要include的代码放到这个文件夹里就行了。
#### 问题：

* 要想以文本形式显示上述语法文本，需要转义一下，如果直接使用“{% include_code ......”这样来显示文本的话，默认会被解析为“Backtick Code Blocks”形式的代码引入，所以需要使用链接1的方式进行转义一下。

### 参考链接
1. [文本形式显示include_code语法](https://github.com/imathis/octopress/blob/site/source/docs/plugins/include-code/index.markdown)