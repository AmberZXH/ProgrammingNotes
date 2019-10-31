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

## 什么是 RESTful API

RESTful 是 “REpresentational State Transfer”， 可翻译为：表现性状态转换。

在 RESTful 中一切都被视为资源，每个资源有对应的 URI 标识。

处理资源使用POST/DELETE/PUT/GET等 HTTP 方法操作实现创建、删除、修改和查询的操作。

客户端通过 4 个 HTTP 动词，对服务器资源进行操作，实现 “表现层状态转换”。

表现是指资源的表现，客户端和服务器之间，传递这种资源的某种表现层。

无状态。每个请求是独立的，从客户端到服务器端的每个请求都必须包含所有的必要信息。

**RESTful API 是一种软件设计风格，主要用于客户端与服务器端交互的软件，一般来说 RESTful API 是基于HTTP协议，通过某个唯一资源标志符（URI）请求对应的服务器资源。通常我们把 RESTful API 编程也叫作接口编程。**

<font color=red>**提示：**</font>

- RESTful API 只是一种设计风格，不是标准，只是提供了一组设计原则和约束条件
- 它主要用户客户端和服务器端交互类的软件
- 它的特点是更简洁，更有层次，更易于实现缓存等机制

------

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


# RESTful API 规范

## 协议

RESTful API 与用户的通信协议，总是使用 HTTP 协议。

## 域名

应尽量将 API 部署在专用的域名之下。

	https://api.exemple.com

如果确定 API 很简单，不会有进一步扩展，可以考虑放在主域名下。

	https://example.com/api/

## 版本

应该将 API 的版本号放入 URL 中

	https://api.example.com/version/

另一种做法是：将版本号放入 HTTP 头信息中，但不如放在 URL 中直观和方便。

## RESTful API 设计六要素 【UMQSER】

**RESTful API 是一种面向资源的设计思想**，主要包括六个方面：

1. 资源路径【URI】
2. HTTP动词【Method】
3. 过滤信息【query-string】
4. 状态码【Status-code】
5. 错误信息【Error】
6. 返回结果【Result】

如图所示：

![3.1示意图](https://raw.githubusercontent.com/CayangPro/my_notes/master/RESTful%20API/img/3.1.jpg)


### 资源路径

路径又称“终点”（endpoint），表示 API 的具体网址。

在 RESTful 架构中，每一个网址代表一种资源（resource），所以，网址中不能有动词，只能有名词，而且名词往往与数据库的表名对应。一般来说，数据库中的表名都是同种记录的“集合”，所以 **API 中的名词也应该使用复数**，比如：

- http://api.local.com/movies  ----表示电影资源
- http://api.local.com/images  ----表示图片资源
- http://api.local.com/musics  ----表示音乐资源
- http://api.local.com/articles  ----表示文章资源

为了方便开发，可以在URI中**加入版本号：**

- http://api.local.com/<font color=red>v1</font>/movies  ----表示电影资源
- http://api.local.com/<font color=red>v1</font>/images  ----表示图片资源
- http://api.local.com/<font color=red>v1</font>/musics  ----表示音乐资源
- http://api.local.com/<font color=red>v1</font>/articles  ----表示文章资源

### HTTP 动词

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

### 过滤信息

在服务端返回的数据很多时，服务器不可能全部返回，此时，我们可以在客户端携带过滤信息，比如：分页信息

例子：

- ?page=1   --- 第一页的信息
- ?offset=10&per_page=10  --  每页10条，偏移10


### 响应状态码

服务端返回的信息，用来告诉客户端操作结果，如：

状态码 | 含义 | 说明
:-|:-:|:-
200 | OK | 操作成功，并返回数据
201 | CREATED | 新建成功
204 | NO CONTENT | 请求成功，删除成功，返回空文档
400 | BAD REQUEST | 请求语法错误
403 | Forbidden | 请求没有权限的资源
404 | NOT FOUND | 请求的资源不存在
500 | INTERNAL SERVER ERROR | 服务器发生不可预期的错误

### 错误处理

如果状态码是 4xx 或者 5xx ，需要告诉客户端对应的错误信息，以json格式返回

	{
		"error": "错误信息",
	}

### 返回结果

根据不同的操作结果，服务器需要返回的结果应该符合这样的规范：

- GET/collections  ---  返回资源列表【数组】
- GET/collections/:id  ---  返回单个资源
- POST/collections  ---  返回新生成的资源
- PUT/collections/:id  ---  返回资源的<font color=red>完整属性</font>
- PATCH/collections/:id  ---  返回被<font color=red>修改的属性 </font>
- DELETE/collections/:id  ---  返回 204 状态码 + 空文档


# RESTful SOAP和RPC的对比

## 概念

### REST

一种架构设计风格，提供了设计原则和约束条件。

### RPC（远程过程调用）

是一种允许分布式应用程序调用网络上不同计算机的可用服务的机制。

其实就是通过一台机器通过一些参数去访问另一台机器上的函数或方法，得到返回结果。

### 远程过程调用发展历程

**第一代：**

- ONC RPC （开放网络计算机的远程过程调用），OSF RPC （开放软件基金会的远程过程调用）

**第二代：**

- CORBA（Common Pbject Request Broker Architecture）即公共对象请求代理体系结构
- DCOM（分布式组件对象模型），COM+
- Java RMI
- .NET Remoting

**第三代：**

- XML-RPC,SOAP,Web Service
- PHPRPC， Hessian， JSON-RPC
- Microsoft WCF， WebAPI
- ZeroClce，Thrift，GRPC
- Hprose

### SOAP

SOAP 即简单对象访问协议， XML-RPC（Simple Object Access Protocol)也叫作简单对象访问协议。

SOAP 是交换数据的一种协议规范，是一种轻量级、简单的、基于 XMl 的协议规范。SOAP 最主要的工作是使用标准的 XML 描述了 RPC 的请求信息（URI/类/方法/参数/返回值）。

理论上， SOAP 就是一段 XML， 可以通过 HTTP/SMTP等发送它，并且 SOAP也是跨语言的。


## 区别

### REST与SOAP

#### 概念方面

REST 接口和 SOAP 接口是两种不同的数据通信接口。

- REST 使用的是 HTTP 协议通信，请求的响应值可以是 xml 也可以是 json
- SOAP 使用的是 SOAP 协议通信，请求和响应都是 xml ，而且有  wsdl 作为请求和响应信息的标准

#### 核心方面

- REST 是基于 HTTP 之上建立的一种接口规范，核心是资源
- SOAP 本身是一种协议，以 xml 格式传输
- SOAP 的 Web 服务是以操作为中心的

#### 协议方面

- RESTful 使用标准的 HTTP 方法（GET/POST/PUT/DELETE）来抽象所有 Web 系统的服务能力
- SOAP 应用都是通过定义自己个性化的接口方法来抽象 Web 服务

#### 安全控制方面

代理服务器可以制定安全策略，一般代理服务器的实现根据（URI、HTTP Method）两元素组来决定 HTTP 请求的安全合法性。当发现 DELETE 这样的请求时，予以拒绝，对于  SOAP ，如果我们想借助于既有的代理服务器进行安全控制，会比较尴尬。因为，代理服务器需要解码 SOAP 的协议，这显然是不合理的。


#### 缓存方面

HTTP GET 请求被设计用来节省客户端与服务端之间的网络传输带来的开销，这也给客户端实现缓存机制提供了可能。

REST 的应用可以充分利于 HTTP 协议对缓存支持的能力。
而所有经过缓存服务器的 SOAP 信息总是 HTTP POST ，缓存服务器如果不解码 SOAP 消息体，就没法知道该 HTTP 请求是否是想从服务器获取数据。 

以上决定了基于 SOAP 应用的缓存机制要远比 REST 复杂。  

**总结：**

基于 REST 构建的系统，其系统的扩展能力要强于 SOAP，这可以体现在它的统一接口抽象、代理服务器支持、缓存服务器支持等多方面。

并且，随着 Web Site as Web Services（网站就是网络服务） 演进的趋势，还有由于 REST 设计实现的简单性和强扩展性的特点。相信，REST 将会成为 Web 服务的一个重要架构实践领域。 


# HTTP 协议简介

## 什么是 HTTP 协议

HTTP 协议是一种基于**请求响应模式**的应用层协议，是一种**无状态**的协议。特点是简捷、快速。通俗的讲，就是一种**一问一答**的模式，前端（浏览器）发送一个 HTTP 请求后，后端（服务器）返回一个 HTTP 响应，如下图所示。

![2.1示意图](https://raw.githubusercontent.com/CayangPro/my_notes/master/RESTful%20API/img/2.1.jpg)

## HTTP 的特点

**1.HTTP 协议是无状态的** 

就是说每次 HTTP 请求都是独立的，任何两个请求之间没有什么必然的联系。

**2.多次 HTTP 请求**

在客户端请求网页时，多数情况下不是一次请求就能成功的，服务端首先响应 HTML 页面，然后浏览器收到响应之后发现 HTML 页面还引用了其他资源，例如：css、js、图片等，还会自动发送 HTTP 请求这些需要的资源。现在的 HTTP 版本支持管道机制，可以同时请求和响应多个请求，大大提高了效率。

**3.基于TCP协议**

HTTP 协议目的是规定客户端和服务端数据传输的格式和数据交互行为，并不负责数据传输的细节。底层是基于 TCP 实现的。现在使用的版本当中是默认持久连接的，也就是多次 HTTP 请求使用一个 TCP 连接。 

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

状态码分类如下：

分类 | 说明 | 描述
:- | :-
1xx | 指示信息 | 表示请求已接收，继续处理
2xx | 成功 | 表示请求已成功接收，理解、接受
3xx | 重定向 | 要完成求情必须进行更进一步的操作
4xx | 客户端错误 | 请求有语法错误或请求无法实现
5xx | 服务器端错误 | 服务器端未能实现合法的请求

### 状态码列表

状态码 | 英文名称 | 描述
:-|:-|:-
100 | Continue | 继续。客户端应继续其请求
101 | Switching Protocols | 切换协议。服务器根据客户端的请求切换协议。只能切换到更高级的协议，例如，切换到HTTP的新版本协议
 | 
200 | OK | 请求成功。一般用于GET与POST请求
201 | Created | 已创建。成功请求并创建了新的资源
202 | Accepted | 已接受。已经接受请求，但未处理完成
203 | Non-Authoritative Information | 非授权信息。请求成功。但返回的meta信息不在原始的服务器，而是一个副本
204 | No Content | 无内容。服务器成功处理，但未返回内容。在未更新网页的情况下，可确保浏览器继续显示当前文档
205 | Reset Content | 重置内容。服务器处理成功，用户终端（例如：浏览器）应重置文档视图。可通过此返回码清除浏览器的表单域
206 | Partial Content | 部分内容。服务器成功处理了部分GET请求
 |
300 | Multiple Choices | 多种选择。请求的资源可包括多个位置，相应可返回一个资源特征与地址的列表用于用户终端（例如：浏览器）选择
301 | Moved Permanently | 永久移动。请求的资源已被永久的移动到新URI，返回信息会包括新的URI，浏览器会自动定向到新URI。今后任何新的请求都应使用新的URI代替
302 | Found | 临时移动。与301类似。但资源只是临时被移动。客户端应继续使用原有URI
303 | See Other | 查看其它地址。与301类似。使用GET和POST请求查看
304 | Not Modified | 未修改。所请求的资源未修改，服务器返回此状态码时，不会返回任何资源。客户端通常会缓存访问过的资源，通过提供一个头信息指出客户端希望只返回在指定日期之后修改的资源
305 | Use Proxy | 使用代理。所请求的资源必须通过代理访问
306 | Unused | 已经被废弃的HTTP状态码
307 | Temporary Redirect | 临时重定向。与302类似。使用GET请求重定向
 | 
400 | Bad Request | 客户端请求的语法错误，服务器无法理解
401 | Unauthorized | 请求要求用户的身份认证
402 | Payment Required | 保留，将来使用
403 | Forbidden | 服务器理解请求客户端的请求，但是拒绝执行此请求
404 | Not Found | 服务器无法根据客户端的请求找到资源（网页）。通过此代码，网站设计人员可设置"您所请求的资源无法找到"的个性页面
405 | Method Not Allowed | 客户端请求中的方法被禁止
406 | Not Acceptable | 服务器无法根据客户端请求的内容特性完成请求
407 | Proxy Authentication Required | 请求要求代理的身份认证，与401类似，但请求者应当使用代理进行授权
408 | Request Time-out | 服务器等待客户端发送的请求时间过长，超时
409 | Conflict | 服务器完成客户端的 PUT 请求时可能返回此代码，服务器处理请求时发生了冲突
410 | Gone | 客户端请求的资源已经不存在。410不同于404，如果资源以前有现在被永久删除了可使用410代码，网站设计人员可通过301代码指定资源的新位置
411 | Length Required | 服务器无法处理客户端发送的不带Content-Length的请求信息
412 | Precondition Failed | 客户端请求信息的先决条件错误
413 | Request Entity Too Large | 由于请求的实体过大，服务器无法处理，因此拒绝请求。为防止客户端的连续请求，服务器可能会关闭连接。如果只是服务器暂时无法处理，则会包含一个Retry-After的响应信息
414 | Request-URI Too Large | 请求的URI过长（URI通常为网址），服务器无法处理
415 | Unsupported Media Type | 服务器无法处理请求附带的媒体格式
416 | Requested range not satisfiable | 客户端请求的范围无效
417 | Expectation Failed | 服务器无法满足Expect的请求头信息
 |
500 | Internal Server Error | 服务器内部错误，无法完成请求
501 | Not Implemented | 服务器不支持请求的功能，无法完成请求
502 | Bad Gateway | 作为网关或者代理工作的服务器尝试执行请求时，从远程服务器接收到了一个无效的响应
503 | Service Unavailable | 由于超载或系统维护，服务器暂时的无法处理客户端的请求。延时的长度可包含在服务器的Retry-After头信息中
504 | Gateway Time-out | 充当网关或代理的服务器，未及时从远端服务器获取请求
505 | HTTP Version not supported | 服务器不支持请求的HTTP协议的版本，无法完成处理


## Content-Type

Content-Type：内容类型，定义网络文件的类型和网页的编码，决定浏览器以什么形式、什么编码读取这个文件。

### application/x-www-form=urlencoded

	POST http://www.example.com HTTP/1.1	
	Content-Type: application/x-www-from=urlendoded;charset=utf-8

	title=test&sub%5bB%5D=1&sub%5B%5D=2&%5bB%5D=3

### multipart/form-data

	POST http://www.example.com HTTP/1.1	
	Content-Type: multipart/form-data; boundary=---webKitFormBoundarryrGKCBY7qhfd3trwa
	
	---webKitFormBoundarryrGKCBY7qhfd3trwa

	Content-Dispostition: form-data;name="text"

	title
	
	---webKitFormBoundarryrGKCBY7qhfd3trwa
	Content-Dispostition: form-data;name="file";filename="test.png"
	Content-Type: image/png

	PNG ... content of test.png
	---webKitFormBoundarryrGKCBY7qhfd3trwa--

### application/json

	POST http://www.example.com HTTP/1.1	
	Content-Type: application/json;charset-utf-8
	
	{"title":"test","sub":[1,2,3]}

### text/xml

	POST http://www.example.com HTTP/1.1	
	Content-Type: text/xml
	
	<!--?xml version="1.0"?-->
	<methodcall>
		<methodname>examples.getStateName</methodname>
		<params>
			<value><i4>41<i4></value>
		</params>
	</methodcall>	

