<font size=6>**RESTful API 学习笔记**</font>

# RESTful API 概述

## 什么是 API

API（Application Programming Interface，应用程序接口）是一些预先定义的函数，或指软件系统不同组成部分衔接的约定。

目的是能够让应用程序或开发人员能够具有访问指定网络资源的能力，而又无需关心访问的源码，或理解内部工作机制的细节。

## API 的产生

随着互联网的发展，尤其是移动互联为代表的 web3.0 时代，客户端层出不穷，以 APP、微信、PC浏览器为代表，服务器业务逻辑是基本一致的。

那么有没有一种方式可以做到<font color=red>**一次编写，随时接入**</font>呢？

目前比较流行的方案是 **RESTful API** 接口开发规范。

![1.1示意图](https://raw.githubusercontent.com/CayangPro/my_notes/master/RESTful%20API/img/1.1.jpg)

## 什么是RESTful API

RESTful API 是一种软件设计风格，主要用于客户端与服务器端交互的软件，一般来说 RESTful API 是基于HTTP协议，通过某个唯一资源标志符（URI）请求对应的服务器资源。通常我们把 RESTful API 编程也叫作**接口编程**。

**扩展1：什么是URI？**

URI(Uniform Resource Identifier）统一资源标志符，URL是URI的一个子集，它是 Uniform Resource Locator 的缩写，译为“统一资源定位符”)。

在 HTTP 协议中，URI 的组成如下：

**Schema://host[:port]/path[?query-string]**

- Schema： 使用的协议类型，如：http/https/ftp等
- host： 主机域名或IP地址
- port： 端口号【可选】
- path： 路径
- query-string： 查询参数【可选】

如：

https://www.baidu.com

https://fanyi.baidu.com/translate?aldtype=16047&query=&key=schema

**扩展2：什么是资源？**

狭义的讲，所有在服务器保存的数据（如：音乐、视频、文章、个人信息等）都是服务器资源；

广义的讲，任何服务器端的对象（如：应用程序、数据库记录、算法等）都可以看做资源；

## RESTful API 的作用

为前端（APP端、PC浏览器端、微信端）提供一个简单并且统一的方式，从后端服务器获取所需的数据，不管是 APP 还是 WEB浏览器，只要通过 HTTP 协议都可以实现同样的操作。


**小结：**

RESTful API 是基于 HTTP 协议实现了通用的的前后端交互，前端通过某个 URI 告诉后端执行对应的操作，并返回所需资源，可以很好的**实现前后端分离**。

# HTTP 协议简介

## 什么是 HTTP 协议

HTTP 协议是一种基于**请求响应模式**的应用层协议，是一种**无状态**的协议。特点是简捷、快速。通俗的讲，就是一种**一问一答**的模式，前端（浏览器）发送一个 HTTP 请求后，后端（服务器）返回一个 HTTP 响应，如下图所示。

![2.1示意图](https://raw.githubusercontent.com/CayangPro/my_notes/master/RESTful%20API/img/2.1.jpg)

## HTTP 请求

HTTP 请求由三个部分组成，分别是：

1. 请求行
2. 请求头
3. 请求体

如图所示

![2.2示意图](https://raw.githubusercontent.com/CayangPro/my_notes/master/RESTful%20API/img/2.2.jpg)

### 请求行

请求行格式如下：

**Method Request-URI HTTP-Version CRLF**

- Method： HTTP 请求类型，如：GET/POST/PUT/DELETE
- Request-URI： HTTP 请求的唯一标识，如：/test.html或/test.php?id=1
- HTTP-Version： HTTP 协议版本，如：HTTP/1.1
- CRLF: 回车换行 CR（回车\n） LF（换行\r）

例子： GET/test.php HTTP/1.1（CRLF）

<font color=red>请求行以“**空格**”分隔，除结尾外的CR和LF外，不允许出现单独的CR或LF字符</font>

### 请求头

请求头包含许多有关前端的环境和请求正文的有用信息。

请求头存在的主要意义是：**将前端的一些信息告诉给后端**，比如 Cookie 信息就是通过请求头由浏览器发送给服务器的。

### 请求体

请求体主要包含前端发送给后端的数据（**参数**）。

对于 GET 请求，一般不需要请求体，因为 GET 参数直接体现在 URL 上。

对于 POST 请求， 需要请求体，请求体里保存 POST 参数，在后端通过 $_POST 超全局数组接收。

### 请求方法

HTTP 的请求方法如下所示：

方法 | 含义
:-|:-
GET | 请求指定的页面信息，并返回实体主体
HEAD | 类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头，只返回响应头信息
POST | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和/或已有资源的修改
PUT | 从客户端向服务器传送的数据取代指定的文档的内容
DELETE | 请求服务器删除指定的资源
CONNECT | HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器
OPTIONS | 允许客户端查看服务器的性能
TRACE | 回显服务器收到的请求，主要用于测试或诊断
PATCH | 是对 PUT 方法的补充，用来对已知资源进行局部更新

<font color=red>**提示：POST/DELETE/PUT/GET: 分别对应 增/删/改/查 四种操作**</font>


**小结：**

HTTP 请求时前端发送给后端的数据，由【请求行、请求头、请求体】三部分组成，用来告诉后端需要执行何种操作，返回何种数据。


## HTTP 响应

HTTP 响应由三个部分组成，分别是：

1. 响应行
2. 响应头
3. 响应体

如图所示

![2.3示意图](https://raw.githubusercontent.com/CayangPro/my_notes/master/RESTful%20API/img/2.3.jpg)


### 响应行

响应行格式如下：

**HTTP-Version Status-Code Reason-Phrase CRLF**

- HTTP-Version： HTTP 协议版本，如：HTTP/1.1
- Status-Code： 响应状态码，如：200/401/500
- Reason-Phrase： 描述信息
- CRLF: 回车换行 CR（回车\n） LF（换行\r）

例子： HTTP/1.1 200 OK


### 响应头

响应头是后端（服务器）返回给前端（客户端）的信息。

**主要用于规定客户端的行为**，换句话说，就是客户端接收到响应后需要执行哪些操作，以什么样的字符（UTF-8）、什么样的格式（Html/json/xml）去解析数据。

**通常在服务端通过 header 函数指定响应头信息**。


### 响应体

响应体是后端（服务器）返回给前端（客户端）的数据。

比如：一个 html 页面代码、一张图片、一个 json 数据...

**小结：**

HTTP 响应式后端（服务器）返回给前端（客户端）的数据，由【响应行、响应头、响应体】三部分组成，主要用于返回前端的请求，操作的执行状态。返回数据，以及在前端接受到数据后如何解析。

## 状态码

- 1xx： 【指示信息】-- 表示请求已接收，继续处理
- 2xx： 【成功】-- 表示请求已成功接收，理解、接受
- 3xx： 【重定向】-- 要完成求情必须进行更进一步的操作
- 4xx： 【客户端错误】-- 请求有语法错误或请求无法实现
- 5xx： 【服务器端错误】-- 服务器端未能实现合法的请求

### 常见状态码

- 200 【OK】： 客户端请求成功
- 400 【Bad Request】： 客户端请求有语法错误，不能被服务器所理解
- 401 【Unauthorized】： 请求未经授权，这个状态码必须和www-Authenticate一起使用
- 403 【Forbidden】： 服务器收到请求，但是拒绝服务
- 404 【Not Found】： 请求的的资源不存在，比如错误的 URL
- 500 【Internal Server Error】： 服务器发生不可预期的错误


# RESTful API 设计六要素 【UMQSER】

**RESTful API 是一种面向资源的设计思想**，主要包括六个方面：

1. 资源路径【URI】
2. HTTP动词【Method】
3. 过滤信息【query-string】
4. 状态码【Status-code】
5. 错误信息【Error】
6. 返回结果【Result】

如图所示：

![3.1示意图](https://raw.githubusercontent.com/CayangPro/my_notes/master/RESTful%20API/img/3.1.jpg)


## 资源路径

在 RESTful API 中，每个资源路径对应唯一的资源，所以规定在网址中不能有动词，只能有名词，并且一般来说为复数，比如：

- http://api.local.com/movies  ----表示电影资源
- http://api.local.com/images  ----表示图片资源
- http://api.local.com/musics  ----表示音乐资源
- http://api.local.com/articles  ----表示文章资源

为了方便开发，可以在URI中**加入版本号：**

- http://api.local.com/<font color=red>v1</font>/movies  ----表示电影资源
- http://api.local.com/<font color=red>v1</font>/images  ----表示图片资源
- http://api.local.com/<font color=red>v1</font>/musics  ----表示音乐资源
- http://api.local.com/<font color=red>v1</font>/articles  ----表示文章资源

## HTTP 动词

对于资源，一般有 4 个操作，即CURD(Create,Update,Retrieve,Delete)增/删/改/查

- GET： 从服务器<font color=red>获取资源</font>（一项或多项）
- POST： 在服务器<font color=red>新建一个资源</font>
- PUT： 在服务器<font color=red>更新资源</font>，服务器返回完整的属性
- DELETE： 从服务器<font color=red>删除资源</font>
- HEAD： 从服务器<font color=red>获取响应头信息</font>
- PATCH： 在服务器<font color=red>更新局部资源</font>，服务器只返回更新的属性

例子：

POST/articles  --- 创建文章内容
GET/articles/1 --- 获取主键id为1的文章

## 过滤信息

在服务端返回的数据很多时，服务器不可能全部返回，此时，我们可以在客户端携带过滤信息，比如：分页信息

例子：

- ?page=1   --- 第一页的信息
- ?offset=10&per_page=10  --  每页10条，偏移10


## 响应状态码

服务端返回的信息，用来告诉客户端操作结果，如：

状态码 | 含义 | 说明
:-|:-:|:-
200 | OK | 操作成功，并返回数据
201 | CREATED | 新建成功
204 | NO CONTENT | 请求成功，删除成功，空文档
400 | BAD REQUEST | 请求语法错误
403 | Forbidden | 请求没有权限的资源
404 | NOT FOUND | 请求的资源不存在
500 | INTERNAL SERVER ERROR | 服务器发生不可预期的错误

## 错误处理

如果状态码是 4xx 或者 5xx ，需要告诉客户端对应的错误信息，以json格式返回

	{
		"error": "错误信息",
	}

## 返回结果

根据不同的操作结果，服务器需要返回的结果应该符合这样的规范：

- GET/collections  ---  返回资源列表【数组】
- GET/collections/:id  ---  返回单个资源
- POST/collections  ---  返回新生成的资源
- PUT/collections/:id  ---  返回资源的<font color=red>完整属性</font>
- PATCH/collections/:id  ---  返回被<font color=red>修改的属性 </font>
- DELETE/collections/:id  ---  返回 204 状态码 + 空文档