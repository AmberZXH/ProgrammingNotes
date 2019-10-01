# <center> Laravle学习笔记 <center> #

## 1. 安装 ##

通过composer安装 

##### `composer create-project --prefer-dist laravel/laravel blog` ####

## 2. 主要目录结构 ##

![目录结构图](/img/mu1.jpg)

## 3. CSRF攻击 ##

### 3.1 什么是CSRF ###

**CSRF**（Cross-site require forgery），中文名称：<font color=red>跨站请求伪造</font>。

&emsp;&emsp;简单的说，就是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并执行一些操作（如发邮件、发消息、转账等）。由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户而去执行。

### 3.2 Laravel如何处理CSRF ###

![原理图](/img/csrf-1.jpg)

定义在**routes/web.php**文件中的路由，默认就有了CSRF验证功能。

![web.php](/img/csrf-2.jpg) 

### 3.3 排除指定路由中不进行csrf验证 ###

并不是所有请求都需要避免CSRF攻击，比如去第三方的API获取数据的请求。

可以通过在<font color=red>VerifyCsrfToken(app/Http/Middleware/VerifyCsrfToken.php)</font>中间件将要排除的请求URL添加到**$except**属性数组中。

![VerifyCsrfToken.php](/img/csrf-3.jpg)


## 4. Laravel路由 ##

### 4.1 什么是路由 ###

将用户的请求按照事先规划的方案提交给指定的控制器和方法来进行处理。