---
title: pelican_生成静态网站
---
# pelican_生成静态网站

Pelican是一套开源的使用Python编写的博客静态生成, 
可以添加文章和和创建页面, 
可以使用MarkDown reStructuredText 和 AsiiDoc 的格式来抒写, 
同时使用 Disqus评论系统, 
支持 RSS和Atom输出, 插件, 主题, 代码高亮等功能, 
采用Jajin2模板引擎, 可以很容易的更改模板


安装
------

可以从github克隆最新的代码安装, 并且建议在virtualenv下使用:

* 建立 python  环境

	使用 virtualenv 

    ```
    virtualenv pelican      # 创建  
    cd pelican  
    sh bin/activate            # 激活虚拟环境
    ```

	使用 codna 创建环境

    ```
    conda env list   
    conda create -n pelican --clone root  
    activate pelican
    ```

	安装 pelican

    ```
    git clone git://github.com/getpelican/pelican.git            # 代码  
    cd pelican  
    python setup.py install
    ```


开始一个博客
------

初始化

    mkdir /path/to/your/blog
    cd /path/to/your/blog
    pelican-quickstart


在回答一系列问题过后你的博客就建成的, 主要生成下列文件:

    .
    |-- content                # 所有文章放于此目录
    |-- develop_server.sh      # 用于开启测试服务器
    |-- Makefile               # 方便管理博客的Makefile
    |-- output                 # 静态生成文件
    |-- pelicanconf.py         # 配置文件
    |-- publishconf.py         # 配置文件


写一篇文章
-----

在 content 目录新建一个 test.md文件, 填入一下内容:

    Title: 文章标题
    Date: 2013-04-18
    Category: 文章类别
    Tag: 标签1, 标签2

    这里是内容

然后执行:

    make html

用以生成html

然后执行

    ./develop_server.sh start

开启一个测试服务器, 这会在本地 8000 端口建立一个测试web服务器, 
可以使用浏览器打开: `http://localhost:8000` 来访问这个测试服务器, 
然后就可以欣赏到你的博客了


创建一个页面
---------
这里以创建 About页面为例

在content目录创建pages目录

    mkdir content/pages

然后创建About.md并填入下面内容

    Title: About Me
    Date: 2013-04-18

    About me content

执行 `make html` 生成html, 然后打开 `http://localhost:8000` 查看效果

让Pelican支持评论
------

Pelican 使用Disqus评论, 可以申请在Disqus上申请一个站点, 然后在pelicanconf.py里添加或修改DISQUS_SITENAME项:

    DISQUS_SITENAME = u"linuxzen"

执行

    make html

更换主题
--------

Pelican本身也提供了一些主题可供选择, 可以从github克隆下来

    git clone git://github.com/getpelican/pelican-themes.git     # 主题


然后在里面找到想要的主题, 然后拷到博客项目当前目录, 这里已neat为例

    cp -r /path/to/themes/from/github/neat .

然后在 pelicanconf.py 配置文件里添加或修改 THEME项为 neat

    THEME = "neat"

重新执行

    make html

使用插件
-------

Pelican 一开始是将插件内置的, 但是新版本 Pelican将插件隔离了出来, 所以我们要到github上 克隆一份新的插件, 在博客目录执行

    git clone git://github.com/getpelican/pelican-plugins.git    # 插件

现在我们博客目录就新添了一个 pelican-plugins目录, 我们已配置sitemap插件为例, sitemap插件可以生成 sitemap.xml 供搜索引擎使用

在pelicanconf.py配置文件里加上如下项:

```python 
PLUGIN_PATH = u"pelican-plugins"

PLUGINS = ["sitemap"]

## 配置sitemap 插件
SITEMAP = {
    "format": "xml",
    "priorities": {
        "articles": 0.7,
        "indexes": 0.5,
        "pages": 0.3,
    },
    "changefreqs": {
        "articles": "monthly",
        "indexes": "daily",
        "pages": "monthly",
    }
}
```

然后再执行

    make html

打开浏览器请求 `http://localhost:8000/sitemap.xml` 即可看到生成的 Sitemap 了


拷贝静态文件
------

如果我们定义静态的文件, 该如何将它在每次生成的时候拷贝到 output 目录呢, 我们以robots.txt为例, 在我们的 content/extra 下面我们放了一个定义好的 robots.txt文件, 在pelicanconf.py更改或添加 FILES_TO_COPY项:


```python 
FILES_TO_COPY = (
    ("extra/robots.txt", "robots.txt"),
)
```

这样在每次生成html的时候都会把 content/extra下的 robots.txt 拷贝到 output目录下

拷贝静态目录
----------

 我们可以在pelicanconf.py里添加或修改 STATIC_PATHS项, 比如我们有个img目录用来放文章所使用的图片, 我们可以在pelicanconf.py添加

 ```python 
STATIC_PATHS = [u"img"]
```


然后 Pelican 就会将 img目录拷贝到 output/static/ 下


部署
--------

上面都弄完之后你就可以得到一个功能健全的博客系统, 接下来就是部署到服务器, 上传到服务器并结合nginx或者apache等web服务器部署这里就不在详述



参考:

* [1]: https://www.cnblogs.com/imhurley/p/6137272.html
* [2]: http://www.uoota.com/blog/archives/27222
* [3]: http://ioreq.com/
* [官方手册]: http://docs.getpelican.com/


