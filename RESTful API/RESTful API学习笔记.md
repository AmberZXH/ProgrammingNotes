<font size=6>**RESTful API 学习笔记**</font>

# RESTful简介

## 背景

随着互联网的发展，尤其是移动互联为代表的 web3.0时代，客户端层出不穷，以APP、微信、PC浏览器为代表，服务器业务逻辑是基本一致的。

那么有没有一种方式可以做到<font color=red>**一次编写，随时接入**</font>呢？

目前比较流行的方案是**RESTful API**接口开发规范

![1.1示意图](https://raw.githubusercontent.com/CayangPro/my_notes/master/RESTful%20API/img/1.1.jpg)

## 什么是RESTful

RESTful 是一种软件设计风格，主要用于客户端与服务器端交互的软件，一般来说 RESTful API 是基于HTTP协议，通过某个唯一资源标志符(URI）请求对应的服务器资源。通常我们把RESTful API编程也叫作**接口编程**。

**扩展1：什么是URI？**

URI(Uniform Resource Identifier）唯一资源标志符，URL是URI的一个子集，它是Uniform Resource Locator的缩写，译为“统一资源定位符”)。

在 HTTP 协议中，URI 的组成如下：

Schema://host[:port]/path[?query-string]

- Schema： 使用的协议类型，如：http/https/ftp等
- host:： 主机域名或IP地址
- port： 端口号【可选】
- path： 路径
- query-string： 查询参数【可选】

如：

https://www.baidu.com

https://fanyi.baidu.com/translate?aldtype=16047&query=&key=schema

**扩展1：什么是资源？**

狭义的讲，所有在服务器保存的数据（如：音乐、视频、文章、个人信息等）都是服务器资源；
广义的讲，任何服务器端的对象（如：应用程序、数据库记录、算法等）都可以看做资源；

## RESTful API的作用

为前端（APP端、PC浏览器端、微信端）提供一个简单并且统一的方式，从后端服务器获取所需的数据，不管是 APP 还是 WEB浏览器，只要通过 HTTP 协议都可以实现同样的操作。

## 总结

RESTful API 是基于 HTTP 协议实现了通用的的前后端交互，前端通过某个 URI 告诉后端执行对应的操作，并返回所需资源，可以很好的**实现前后端分离**。