---
title: chrome_不允许访问的端口
date: 2019-05-19 17:10:37
tags:
---



项目本地开发的api服务端口 是6000

使用 firefox 和chrome 访问都没反应

在地址栏直接访问报错 

unsafe-port 

把端口改成 8080 可以访问了


ajax 跨域时不能使用cookie 
--------

浏览器报错:

原因：凭据不支持，如果 CORS 头 ‘Access-Control-Allow-Origin’ 为 ‘*

参考: 

[1]: https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS/Errors/CORSNotSupportingCredentials

这是因为浏览器不让跨域请求带 cookie , 

所以设置ajax 的参数 

withCredentials: false,


如果要使用 cookie , 需要修改服务端 的 Access-Control-Allow-Origin 

If, instead, you need to adjust the server's behavior, you'll need to change the value of Access-Control-Allow-Origin to grant access to the origin from which the client is loaded.

