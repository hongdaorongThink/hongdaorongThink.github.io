---
title: pyqt5_下载一个demo
date: 2019-05-10 12:04:54
tags:
---


想知道pyqt5 的demo 代码 


官方网站
-----

* [官网]: https://www.riverbankcomputing.com/news
* [下载页面]: https://www.riverbankcomputing.com/software/pyqt/download5
* [手册]: https://www.riverbankcomputing.com/static/Docs/PyQt5/index.html 
* [手册_模块列表]: https://www.riverbankcomputing.com/static/Docs/PyQt5/module_index.html
* [手册_类列表]: https://www.riverbankcomputing.com/static/Docs/PyQt5/sip-classes.html

官方 examples
-------

一个是 源码里面的 PyQt5_gpl-5.12.2\examples\

从官网下载, 放到百度云了 

链接：https://pan.baidu.com/s/1BurYuZmkmIPWFtIs-7SEkg 
提取码：5lyx 


使用命令 

    python C:\program1\qttools\PyQt5_gpl-5.12.2\examples\qtdemo\qtdemo.py


giee上找的一个代码
------

地址 : https://gitee.com/ranzechen/HelpTool

常用工具的一个项目,

下载源码:

    git clone https://gitee.com/ranzechen/HelpTool.git

该项目依赖 

    markdown
    PyExecJs
    pysha3
    qrcode
    pypinyin
    wmi

执行 :

    pip install markdown
    pip install PyExecJs
    pip install pysha3
    pip install qrcode
    pip install pypinyin
    pip install wmi

因为是在conda 的一个env 中执行的, 写了一个启动脚本:

helpTool.bat 

```bat
cmd.exe /k "C:\ProgramData\Anaconda3\Scripts\activate.bat pelican&&cd D:\repo\HelpTool&& D: &&python D:\repo\HelpTool\HelpTool.py"
```



giee上找的一个代码 -> PyQt5快速开发与实战
------

地址 : https://gitee.com/piaolingtear/PyQt5LivingExample

常用工具的一个项目,

下载源码:

    git clone https://gitee.com/piaolingtear/PyQt5LivingExample.git

用python 打开每个目录的主文件查看


