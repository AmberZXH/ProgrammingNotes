<font size=6>**Laravle学习笔记**</font>

# 安装 #

## 通过composer安装 ##

	composer create-project --prefer-dist laravel/laravel blog

# 目录结构 #

## 主要目录结构 ##

Laravel框架主要目录及作用如下图所示

![目录结构图](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/mu1.JPG)

# CSRF攻击 #

## 什么是CSRF ##

**CSRF**（Cross-site require forgery），中文名称：<font color=red>跨站请求伪造</font>。

简单的说，就是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并执行一些操作（如发邮件、发消息、转账等）。由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户而去执行。


## Laravel如何处理CSRF ##

![原理图](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/csrf-1.JPG)

定义在<font color=red>routes/web.php</font>文件中的路由，默认就有了CSRF验证功能。

![web.php](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/csrf-2.JPG) 

## 排除指定路由中不进行csrf验证 ##

并不是所有请求都需要避免CSRF攻击，比如去第三方的API获取数据的请求。

可以通过在<font color=red>VerifyCsrfToken</font>(<u>app/Http/Middleware/VerifyCsrfToken.php</u>)中间件将要排除的请求URL添加到**$except**属性数组中。
	
	class VerifyCsrfToken extends Middleware
	{
	    protected $except = [
	        //把要排除的规则写此数组中
	        '/user'
	    ];
	}


# Laravel路由 #

## 什么是路由 ##

将用户的请求按照事先规划的方案提交给指定的控制器和方法来进行处理。
![route qequest](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/route-1.png)

## 路由配置文件 ##

web项目路由位置： 项目目录<u>/routes/web.php</u>

api项目路由位置： 项目目录<u>/routes/api.php</u>


## 4种基础路由的定义【<font color=red>重要</font>】 ##

1.Route::请求方式('uri',匿名函数);

	Route::get('/user', function () {
    	return view('welcome');
	});

2.Route::请求方式('uri','控制器名称@操作方法');

	Route::get('/user', 'UserController@index');

3.从服务器取出资源（一项或多项） select ---> Route::get($rui, $callback);

	Route::get('/user', function () {
    	return '这是GET请求';
	});

4.在服务器新建一个资源 ---> Route::post($rui, $callback);

	Route::post('/user', function () {
	    return '这是POST请求';
	});
	
5.在服务器更新资源 ---> Route::put/puth($rui, $callback);

	Route::put('/user', function () {
	    return '这是PUT请求';
	});

6.从服务器删除资源 ---> Route::delete($rui, $callback);

	Route::delete('/user', function () {
	    return '这是DELETE请求';
	});


**注意：<font color=red>4 种基础路由中，只有GET方式不进行CSRF验证，其他请求方式则需要CSRF验证</font>**

## 其他路由的定义 ##

### 一次多个请求类型 ###

	//match 可以一次写多个请求类型
	//它有三个参数，第1个参数是数组类型   第2个是 uri   第3个是回调函数或控制器
	Route::match(['get','post','put'],'/user',function (){
	    //打印方法
	    dump($_SERVER);
	});

### 所有请求类型 ###

注意实际工作中不推荐此方法来写路由

	//获取所有类型
	//实际工作中不推荐用此方法来写路由
	Route::any('/user',function (){
	    //打印方法
	    dump($_SERVER);
	});

## 路由参数 ##

### 参数必填 ###

Route::get('URI/<font color=red>{参数名称}</font>','闭包或控制器相应方法标识');

	Route::get('article/{id}',function ($id){
	    echo '文章id:'.$id;
	});

### 参数可选 ###

Route::get('URI/<font color=red>{参数名称?}</font>','闭包或控制器相应方法标识');

	Route::get('article/{id?}',function ($id){
	    echo '文章id:'.$id;
	});

### 参数限制 ###

Route::get('URI/<font color=red>{参数名称}</font>','闭包或控制器相应方法标识')<font color=red>->where(['参数名称'=>'正则']);</font>

	Route::get('article/{id}',function ($id){
	    echo '文章id:'.$id;
	})->where(['id'=>'\d+']);

	//还可以在参数用处PHP7之后的参数限定
	Route::get('article/{id}',function (int $id){
	    echo '文章id:'.$id;
	});

## 路由别名【掌握】 ##

作用：

- 方便管理生成的URL地址
- 控制权限

### 定义路由别名 ###

Route::get('URI','闭包或控制器相应方法标识')<font color=red>->name('路由别名');</font> ，路由别名的形式可以自由定义，此处用 . 连接

	Route::get('user',function(){
		return 'get	请求';
	})->name('index.user');

### 使用路由别名 ###

	Route::get('user',function(){
		//用路由别名得到url地址
		return route('index.user');
	});

## 路由分组 ##

### 路由前缀 ###

比如后台有如下路由

- /admin/login
- /admin/logout
- /admin/user/add
- admin/user/del

在以上的路由地址中admin为相同路由前缀，此时可使用路由前缀进行路由分组。

**提示：**分组中还可以进行分组，无限分组下去，一般2~3级就可以了。

	Route::post('admin/login',function (){
	    return 1;
	});
	
	Route::post('admin/logout',function (){
	    return 2;
	});
	
	Route::post('admin/user/add',function (){
	    return 3;
	});
	
	Route::post('admin/user/del',function (){
	    return 4;
	});

	//路由分组之前缀
	//参数1  数组
	//参数2 匿名函数 参数就是Route对象
	Route::group(['prefix'=>'admin'],function (){
	    Route::post('login',function (){
	        return 1;
	    });
	    Route::post('logout',function (){
	        return 2;
	    });
		//匿名函数 参数就是Route对象
	    Route::group(['prefix'=>'user'],function ($route){
	        $route::post('add',function (){
	            return 3;
	        });
	        Route::post('del',function (){
	            return 4;
	        });
	    });
	});

### 分组命名空间 ###

	Route::group('namespace'=>'Admin',function(){
		Route::get('user','UserController@index')->name('user.center');	
	});

## 查看定义好的路由 ##

	php artisan route:list

![查看已定义路由](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/route-3.jpg "查看已定义路由")

# 控制器 #

## 控制器文件位置 ##

Laravel控制器放在在<u>app/http/controllers/</u>文件夹中。

## 控制器命名规范 ##

Laravel控制器采用<font color=red>大驼峰命名规范</font>，同时注意命名空间个基类控制器的引入

## 控制器创建 ##

### 一般控制器的创建 ###

**php artisan make:controller 控制器名Controller**

	//命令
	> php.exe F:\wamp\www\web\Laravel\weblaravel\artisan make:controller LoginController
	Controller created successfully.

	//执行结果	
	<?php
	
		namespace App\Http\Controllers;
		
		use Illuminate\Http\Request;
		
		class LoginController extends Controller
		{
		    //
		}

### 资源控制器的创建 ###

**php artisan make:controller 控制器名Controller -r或者--resource**

	//命令	
	> php.exe F:\wamp\www\web\Laravel\weblaravel\artisan make:controller ArticleController -r
	Controller created successfully.

	//执行结果
	<?php

	namespace App\Http\Controllers;
	
	use Illuminate\Http\Request;
	
	class ArticleController extends Controller
	{
	    /**
	     * Display a listing of the resource.
	     *
	     * @return \Illuminate\Http\Response
	     */
	    public function index()
	    {
	        //
	    }
	
	    ...
	
	    /**
	     * Remove the specified resource from storage.
	     *
	     * @param  int  $id
	     * @return \Illuminate\Http\Response
	     */
	    public function destroy($id)
	    {
	        //
	    }
	}

### 分目录下创建控制器 ###

**php artisan make:controller 目录名/控制器名Controller -r或者--resource**
	
	//命令
	> php.exe F:\wamp\www\web\Laravel\weblaravel\artisan make:controller Admin/userController
	Controller created successfully.

	//执行结果
	<?php
	namespace App\Http\Controllers\Admin;
	
	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;
	
	class userController extends Controller
	{
	    //
	}

## 定义路由访问控制器中的方法 ##

**Route::请求方法（URI, '[命名空间\]控制器类名@方法名'）**，如果类在Controller根目录下，就可以不用加命名空间，框架会自动加上。

	Route::get('/login','LoginController@index')->name('login');

如果在Controller下面有目录中的控制器类，则路定义由时一定要加上命名空间。

	Route::get('user','Admin\UserController@index')->name('user.center');

分组简化命名空间

	Route::group('namespace'=>'Admin',function(){
		Route::get('user','UserController@index')->name('user.center');	
	});
	
# 请求 #

Laravel框架为我们提供了多种获取数据的形式，常用的形式有如下两种。

## input类来获取数据 ##

通过接受用户输入的类：<u>Illuminate\Support\Facades\Input</u>来进行数据获取：

1. Input::get('参数名'，'如果没有传递参数使用该默认值')
2. Input::all();  获取所有的用户的输入
3. Input::only([]);  获取指定几个用户的输入（白名单）
4. Input::except([]); 获取指定几个用户的输入以外的所有参数（黑名单）
5. Input::has('名称'); 判断某个输入的参数是否存在

<font color=red>**以上方法既可以后去get中的信息，也可以获取post中的信息** </font>

	<?php

	namespace App\Http\Controllers\Admin;
	
	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;
	// Input类的导入
	use Illuminate\Support\Facades\Input;
	
	class UserController extends Controller
	{
	    //获取用户数据
	    public function getInfo(){
	        // get获取请求的数据
	        // 参数1：请求名  参数2：默认值
	        $userName = Input::get('username','张三');
	        $pass = Input::get('password', 'admin1111');
	        //return $userName.' | '.$pass;
	
	        // 获取全部
	        dump(Input::all());
	
	        // 获取指定字段 （白名单）
	        dump(Input::only(['password']));
	
	        // 获取排除的字段
	        dump(Input::except('password'));
	
	        // 判断某个输入的参数是否存在
	        dump(Input::has('sex')); //返回 true或 false
	    }
	}

## Request获取数据【掌握】 ##

由于获取数据功能太过于常用，所以Laravel又以一个依赖注入的方式，帮我们实例获取对象。

	<?php
	
	namespace App\Http\Controllers;
	
	use Illuminate\Http\Request;
	
	class LoginController extends Controller
	{
	    // 登录处理 依赖注入方式获取数据
	    public function login(Request $request)
	    {
	        //获取指定字段
	        $username = $request->get('username','null');
	        $username = $request->get('password','null');
	
	        //获取全部
	        dump($request->all());
	
	        //获取指定字段 白名单
	        dump($request->only(['username','password']));
	        
	        //排除不要的字段 黑名单
	        dump($request->except(['username']));
	
	        //判断一个字段是否存在
	        dump($request->has('sex'));  //返回 true/false
			
			//判断请求类型
        	dump($request->isMethod('post'));
	    }
	
	}

$request类还可以获取cookie 和文件上传，当用到Route::match() 就一定会用到 $request 中的  isMethod 方法,对请求类型进行判断：

在路由中定义路由

	Route::match(['get','psot'], 'login', 'LoginController@login');


控制器中进行判断

	//判断请求类型
    if($request->isMethod('post')){
        return '这是post请求';
    }

## 辅助函数 request() 获取数据 ##

request()返回的就是 Request 类对象，控制器中用辅助函数request()获取数据：
	
	<?php
	
	namespace App\Http\Controllers;
	
	use Illuminate\Http\Request;
	
	class LoginController extends Controller
	{    
	
	    // 登录处理 辅助函数request()获取数据
	    public function login2()
	    {
	        //获取指定字段
	
	        //获取全部
	        dump(request()->all());
	
	        //获取指定字段 白名单
	        dump(request()->only(['username','password']));
	
	        //排除不要的字段 黑名单
	        dump(request()->except(['username']));
	
	        //判断一个字段是否存在
	        dump(request()->has('sex'));  //返回 true/false
	
	        //判断请求类型
	        dump(request()->isMethod('post'));
	    }
	
	}

# 响应 #



	