---
title: 初识Web
date: 2020-04-20 19:55:33
categories: 技术会刊
tags:
---

# B/S架构
客户端(Browser)：IE、Edge、Chrome
服务端（Server）：IIS、Tomcat、Netty

## 架构概述
客户端 -> URL -> HTTP -> 服务器 -> HTML -> 浏览器渲染

### 如何发起请求
在浏览器中输入URL？
1、Web请求是基于HTTP的，而HTTP又是基于TCP/IP。
2、URL（统一资源定位符）：通过DNS进行解析URL为IP地址，通过IP地址；
（1）通过本地查询，浏览器会查询缓存中是否有域名对应的IP地址；没有则‪查找操作系统中对应的域名访问地址，Windows：C:\Windows\System32\drivers\etc\hosts（使用ipconfig/flushdns 刷新缓存），Linux：/ect/hosts；（通过/etc/init.d/nscd restart）；
（2）重DNS服务器获取IP地址，优先从LDNS（本地域名服务器）查询，没有则从Root Server域名服务器获取IP地址。
3、创建HTTP报文：
（1）请求行（request line）：请求方法（get、post）、URL、协议版本（HTTP/1.1）
（1）请求头（header）:Accept-Charset、User-Agent、Connection（Keep-Alive）
（2）请求体（body）。
4、服务器端接收HTTP请求，处理并返回数据（HTML）。
5、浏览器接收数据，渲染展示内容。

## 浏览器缓存
有304的状态码，通过Ctrl+F5强制刷缓存。
1、Cache-Control/Pragma：设置浏览器如何处理缓存，浏览器支撑好，优先级高。max-age:xxx，意思是缓存内容xxx秒后失效，Pragma:no-cache同Cache-Control:no-cache。
2、Expires：设置缓存过期时间，过期后则重写想服务器发起请求。
3、Last-Modified：用于表示服务器资源的修改时间，比如第一次访问服务器返回Last-Modified:xxx，则客户端第二次请求的时候则会询问If-Modified-Since:xxx，如果满足则返回304状态码。

## Session与Cookie
由于HTTP是无状态的，所以就引入了Seesion和Cookie，他们的作用都是用于用户保持。
（1）Cookie是存储在客户端，默认是在Http请求头的，如果浏览器不支持Cookie，则会在URL参数中。
（2）Session是存储在服务器端，通过Cookie来对应服务器端的Session，识别用户。 在web.xml中配置session-config，来制定SessionCookieName，默认是“JESSIONID”。

## 分布式Seesion
1、简单粗暴：存储在客户端Cookie中，但缺点不太多，不安全、客户端限制等。
2、IPHash：保证某一个客户端IP始终访问一个后端服务器。
3、Session共享：通过Redis等实现Session共享。
