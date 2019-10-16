<font size=6>**Laravle5.6学习笔记**</font>

# 安装

## 通过composer安装

	composer create-project --prefer-dist laravel/laravel blog

## 安装laravel-debugbar调试工具

1. 在 packagist 中搜索 laravel-debugbar
2. 项目中执行 composer require barryvdh/laravel-debugbar
3. 在config/app.php加入： `Barryvdh\Debugbar\ServiceProvider::class，`
4. 线上时注释掉第三步的代码


# 目录结构

## 主要目录结构

Laravel框架主要目录及作用如下图所示

![目录结构图](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/mu1.JPG)

# CSRF攻击

## 什么是CSRF

**CSRF**（Cross-site require forgery），中文名称：<font color=red>**跨站请求伪造**</font>。

简单的说，就是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并执行一些操作（如发邮件、发消息、转账等）。由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户而去执行。


## Laravel如何处理CSRF

![原理图](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/csrf-1.JPG)

定义在<font color=red> routes/web.php </font>文件中的路由，默认就有了CSRF验证功能。

![web.php](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/csrf-2.JPG) 

## 排除指定路由中不进行CSRF验证

并不是所有请求都需要避免CSRF攻击，比如去第三方的API获取数据的请求。

可以通过在 <font color=red>VerifyCsrfToken</font>(<u>app/Http/Middleware/VerifyCsrfToken.php</u>) 中间件将要排除的请求URL添加到**$except**属性数组中。
	
	class VerifyCsrfToken extends Middleware
	{
	    protected $except = [
	        //把要排除的规则写此数组中
	        '/user'
	    ];
	}


# Laravel路由

## 什么是路由

将用户的请求按照事先规划的方案提交给指定的控制器和方法来进行处理。
![route qequest](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/route-1.png)

## 路由配置文件

web项目路由位置： 项目目录 <u>/routes/web.php</u>

api项目路由位置： 项目目录 <u>/routes/api.php</u>


## 4种基础路由的定义【<font color=red>重要</font>】

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
	
5.在服务器更新资源 ---> Route::put/patch($rui, $callback);

	Route::put('/user', function () {
	    return '这是PUT请求';
	});

6.从服务器删除资源 ---> Route::delete($rui, $callback);

	Route::delete('/user', function () {
	    return '这是DELETE请求';
	});


**注意：<font color=red>4 种基础路由中，只有GET方式不进行CSRF验证，其他请求方式则需要CSRF验证</font>**

## 其他路由的定义

### 一次多个请求类型

	//match 可以一次写多个请求类型
	//它有三个参数，第1个参数是数组类型   第2个是 uri   第3个是回调函数或控制器
	Route::match(['get','post','put'],'/user',function (){
	    //打印方法
	    dump($_SERVER);
	});

### 所有请求类型

注意实际工作中不推荐此方法来写路由

	//获取所有类型
	//实际工作中不推荐用此方法来写路由
	Route::any('/user',function (){
	    //打印方法
	    dump($_SERVER);
	});

## 路由参数

### 参数必填

Route::get('URI/<font color=red>{参数名称}</font>','闭包或控制器相应方法标识');

	Route::get('article/{id}',function ($id){
	    echo '文章id:'.$id;
	});

### 参数可选

Route::get('URI/<font color=red>{参数名称?}</font>','闭包或控制器相应方法标识');

	Route::get('article/{id?}',function ($id){
	    echo '文章id:'.$id;
	});

### 参数限制

Route::get('URI/<font color=red>{参数名称}</font>','闭包或控制器相应方法标识')<font color=red>->where(['参数名称'=>'正则']);</font>

	Route::get('article/{id}',function ($id){
	    echo '文章id:'.$id;
	})->where(['id'=>'\d+']);

	//还可以在参数用处PHP7之后的参数限定
	Route::get('article/{id}',function (int $id){
	    echo '文章id:'.$id;
	});

## 路由别名【掌握】

作用：

- 方便管理生成的URL地址
- 控制权限

### 定义路由别名

Route::get('URI','闭包或控制器相应方法标识')<font color=red>->name('路由别名');</font> ，路由别名的形式可以自由定义，此处用 . 连接

	Route::get('user',function(){
		return 'get	请求';
	})->name('index.user');

### 使用路由别名

	Route::get('user',function(){
		//用路由别名得到url地址
		return route('index.user');
	});

## 路由分组

### 路由前缀

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

### 分组命名空间

	Route::group('namespace'=>'Admin',function(){
		Route::get('user','UserController@index')->name('user.center');	
	});

## 查看定义好的路由

	php artisan route:list

![查看已定义路由](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/route-3.jpg "查看已定义路由")

# 控制器

## 控制器文件位置

Laravel控制器放在在 <u>app/http/controllers/</u> 文件夹中。

## 控制器命名规范

Laravel控制器采用 <font color=red>**大驼峰命名规范**</font>，同时注意命名空间和基类控制器的引入

## 控制器创建

### 一般控制器的创建

	php artisan make:controller 控制器名Controller

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

### 资源控制器的创建

	php artisan make:controller 控制器名Controller -r或者--resource 

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

### 分目录下创建控制器

	php artisan make:controller 目录名/控制器名Controller -r或者--resource
	
	//命令
	> php.exe F:\wamp\www\web\Laravel\weblaravel\artisan make:controller Admin/UserController
	Controller created successfully.

	//执行结果
	<?php
	namespace App\Http\Controllers\Admin;
	
	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;
	
	class UserController extends Controller
	{
	    //
	}

## 定义路由访问控制器中的方法

**Route::请求方法（URI, '[命名空间\]控制器类名@方法名'）**，如果类在 Controllers 根目录下，就可以不用加命名空间，框架会自动加上。

	Route::get('/login','LoginController@index')->name('login');

如果在 Controllers下面有目录中的控制器类，则路定义由时一定要加上命名空间。

	Route::get('user','Admin\UserController@index')->name('user.center');

分组简化命名空间

	Route::group('namespace'=>'Admin',function(){
		Route::get('user','UserController@index')->name('user.center');	
	});
	
# 请求

Laravel框架为我们提供了多种获取数据的形式，常用的形式有如下两种。

## Input类来获取数据

通过接受用户输入的类： <u>Illuminate\Support\Facades\Input</u> 来进行数据获取：

1. Input::get('参数名'，'如果没有传递参数使用该默认值')
2. Input::all();  获取所有的用户的输入
3. Input::only([]);  获取指定几个用户的输入（白名单）
4. Input::except([]); 获取指定几个用户的输入以外的所有参数（黑名单）
5. Input::has('名称'); 判断某个输入的参数是否存在

<font color=red>**以上方法既可以获取get中的信息，也可以获取post中的信息，使用时注意Input类的导入：use Illuminate\Support\Facades\Input;** </font>

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

## Request获取数据【掌握】

由于获取数据功能太过于常用，所以 Laravel 又以一个依赖注入的方式，帮我们实例获取对象。

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

## 辅助函数 request() 获取数据

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


# 响应


当服务器收到浏览器的请求后，会发送响应消息给浏览器。

## 返回字符串

在控制器或路由中 echo 或 return 一个字符串就可以了。 

## 设置cookie

Laravel框架为了安全，它的cookie是加密的。注意response()中必须有内容，没有也要填一个空字符串，如：response('')

	// 设置 cookie 
	return response('')->cookie('id',111,10.'/');
	// 读取 cookie
	echo request->cookie('id');

## 重定向

根据路由的别名，跳转到指定的位置：

	retrn redirect()->route('user.center');
	//另一种写法
	return redirect(route(''user.center'));

	//带参数
	return redirect()->route('user.cneter',['id'=>1]);
	return redirect(route(''user.center',['id'=>1]));

## Json数据返回
	
	//参数1 数组
	//状态码，默认200
	return response()->json(['id'=>1,'name'=>'张三']，201);

# 视图

视图的作用就是用来存放应用程序中 HTML 内容，并且能够将你的控制器层与展示层分开。我们在控制器中使用助手函数 view() 来加载视图模板。

## 视图目录位置

视图文件放置在 <u>项目目录/resources/views</u> 文件夹中

## 视图命名

视图文件是以 .blade.php为后缀名

	// 一定要写模板的名称  模板在 /resources/views/index.blade.php
    return view('index');

如果视图文件是在 resources/views</u> 下的不同模块中，如在 resources/views/html/</u> 文件夹中，可按照如下写法。提示：可以多层目录。

	return view('html.index'); // 推荐写法
    return view('html/index'); // 不推荐

## 分配数据到模板

### 关联数组的形式传值
	
	//控制器中
	$data = ['id'=>1,'name'=>'张三'];
    return view('html.index',$data);
	//模板中
	<p>ID：{{ $id }}</p>
    <p>Name：{{ $name }}</p>
	
	//或者
	//控制器中
	$data = ['id'=>1,'name'=>'张三'];
    return view('html.index',['data'=>$data]);	
	//模板中
	<p>ID：{{ $data['id'] }}</p>
    <p>Name：{{ $data['name'] }}</p>

### compact【推荐】

	//控制器中
	$data = ['id'=>1,'name'=>'张三'];
    return view('html.index',compact('data'));
	//模板中
	<p>ID：{{ $data['id'] }}</p>
    <p>Name：{{ $data['name'] }}</p>

### with

	//控制器中
	$data = ['id'=>1,'name'=>'张三'];
    return view('html.index')->with(['data'=>$data]);
	//模板中
	<p>ID：{{ $data['id'] }}</p>
    <p>Name：{{ $data['name'] }}</p>

## 模板中输出变量

{{$变量名}}，如：{{$name}}

	<p>Name：{{ $data['name'] }}</p>

### 三元运算

{{ $name or 'default' }} 等价于 <?php> echo isset($name)?$name:'default' ?>

	<p>年龄：{{ $data['age'] or '没有定义' }}</p>
	{{-- 建议在laravel5.7之后用 --}}  // Blade模板引擎注释风格
    <p>年龄：{{ $data['age'] ?? '没有定义' }}</p>

### 未转义输出

如果变量信息里有html标记信息，在输出的时候html标记会被转化为实体符号，而没有被浏览器解析掉，如果希望看到被浏览器解析后的内容，就需要设置两个<font color=red>俩个感叹号</font>，如：{!! $变量名 !!}

	//控制器中
	$data = ['id'=>1,'name'=>'张三'];
    $tite = "<a href='www.baidu.com'>百度一下</a>";
    return view('html.index',compact('data','title'));
	//模板中
	<p>{!! $title !!}</p>

### 原始形态输出

由于很多 Javascript 框架都使用花括号来表明所提供的表达式，所以你可以使用 @ 符号来告知Blade渲染引擎你需要保留这个表达式的原始形态，如：@{{ $变量名 }}

## 使用函数

可以在 Blade 模板中直接使用 php 函数，{{php函数名}}

	<p>{{ date('Y-m-d') }}</p>

## if语句

	<div>
	    <h3>条件判断</h3>
	    @if($age <= 18)
	        <h4>少年</h4>
	    @elseif( $age <= 40 )
	        <h4>青年</h4>
	    @elseif( 40 > $age )
	        <h4>中年</h4>
	    @endif
	</div>

## Switch语句

可以使用 @switch、@case、@break、@default 和 @endswitch 指令来构建 Switch 语句：

	@switch($i)
	    @case(1)
	        First case...
	        @break
	
	    @case(2)
	        Second case...
	        @break
	
	    @default
	        Default case...
	@endswitch

## 循环

### for循环

	@for ($i = 0; $i < 10; $i++)
	    目前的值为 {{ $i }}
	@endfor

### foreach循环

	@foreach ($users as $user)
	    <p>此用户为 {{ $user->id }}</p>
	@endforeach

### forelse循环

	@forelse ($users as $user)
	    <li>{{ $user->name }}</li>
	@empty
	    <p>没有用户</p>
	@endforelse

### while循环

	@while (true)
	    <p>死循环了</p>
	@endwhile

### 循环其他事项

循环时，你可以使用 循环变量 来获取循环的信息，例如是否在循环中进行第一次或最后一次迭代。

当使用循环时，你也可以结束循环或跳过当前迭代：

	@foreach ($users as $user)
	    @if ($user->type == 1)
	        @continue
	    @endif
	
	    <li>{{ $user->name }}</li>
	
	    @if ($user->number == 5)
	        @break
	    @endif
	@endforeach

你还可以使用一行代码包含指令声明的条件：

	@foreach ($users as $user)
	    @continue($user->type == 1)
	
	    <li>{{ $user->name }}</li>
	
	    @break($user->number == 5)
	@endforeach

### 循环变量

循环时，可以在循环内使用 $loop 变量。这个变量可以提供一些有用的信息，比如当前循环的索引，当前循环是不是首次迭代，又或者当前循环是不是最后一次迭代：

	@foreach ($users as $user)
	    @if ($loop->first)
	        这是第一个迭代。
	    @endif
	
	    @if ($loop->last)
	        这是最后一个迭代。
	    @endif
	
	    <p>This is user {{ $user->id }}</p>
	@endforeach

在一个嵌套的循环中，可以通过使用 $loop 变量的 parent 属性来获取父循环中的 $loop 变量：

	@foreach ($users as $user)
	    @foreach ($user->posts as $post)
	        @if ($loop->parent->first)
	            This is first iteration of the parent loop.
	        @endif
	    @endforeach
	@endforeach

$loop 变量也包含了其它各种有用的属性：

属性 | 描述
:-|:-
$loop->index | 当前循环迭代的索引（从0开始）。
$loop->iteration | 当前循环迭代 （从1开始）。
$loop->remaining | 循环中剩余迭代数量。
$loop->count | 迭代中的数组项目总数。
$loop->first | 当前迭代是否是循环中的首次迭代。
$loop->last | 当前迭代是否是循环中的最后一次迭代。
$loop->depth| 当前循环的嵌套级别。
$loop->parent | 在嵌套循环中，父循环的变量。

## 模板包含 

你可以使用 Blade 的 @include 命令来引入一个已存在的视图，所有在父视图的可用变量在被引入的视图中都是可用的。

	<div>
		// public 表示 views 下的 public目录
		// header 表示在 views/public/heade.blade.php文件
	    @include('public.header') 
	
	    <form>
	        <!-- 表单内容 -->
	    </form>
	</div>

被引入的视图会继承父视图中的所有数据，同时也可以向引入的视图传递额外的数组数据：

	@include('view.name', ['some' => 'data']);

当然，如果尝试使用 @include 去引入一个不存在的视图，Laravel 会抛出错误。如果想引入一个可能存在或可能不存在的视图，就使用 @includeIf 指令:

	@includeIf('view.name', ['some' => 'data']);

如果要根据给定的布尔条件 @include 视图，可以使用 @includeWhen 指令：

	@includeWhen($boolean, 'view.name', ['some' => 'data']);

要包含来自给定数组视图的第一个视图，可以使用 includeFirst 指令：

	@includeFirst(['custom.admin', 'admin'], ['some' => 'data']);

**提示：** 请避免在 Blade 视图中使用  __DIR__ 及 __FILE__ 常量，因为它们会引用编译视图时缓存的位置。 

## 模板继承 

在一个项目中有许多模板文件，它们们有一个特点：拥有共同的头部和底部内容。为了避免相同代码重复开发、维护，可以把共同的头部和底部类容集中到一个布局文件中，之后各个具体的模板文件去继承该布局文件而使用头部和底部内容，这就是模板继承。

	// 继承 resource/views/layouts/home.blade.php布局文件
	@exrends('laoyouts.home')
	// 使用 section 标签替换布局模板中的可变区域
	@section('content')
		给布局文件 yield('content')的区域进行内容填充 
	@endsection

父级模板文件中定义
	
	<!doctype html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <meta name="viewport"
	          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
	    <meta http-equiv="X-UA-Compatible" content="ie=edge">
	    <title>@yield('title')</title>
	    @yield('css')
	</head>
	<body>
	
	<div>
	    <div>头部</div>
	    @yield('content')
	    <div>底部</div>
	</div>
	
	</body>
	</html>

继承的子模板中使用

	//继承公共的父级模板
	@exrends('laoyouts.home')
	{{-- 用法1 --}}
	@section('title','content')
	{{-- 用法2 双标签 --}}
	@section('content')
		your content
	@endsection 

# 表单验证

表单验证是为了防止访问者跳过客户端验证而造成系统安全问题，一旦非法用户绕过客户端验证而服务器端没有验证，就很不安全，所以项目必须要进行服务器端表单验证。

Laravel提供了多种不同的验证方法来对应用程序传入的数据进行验证。

## 验证规则

<font color=red>注：多个验证规则可以通过 | 字符进行隔开。</font>

以下为部分常用验证规则：

规则 | 说明
:- | :-
required | 不能为空
max:value | 字段值必须 <= value，对于字符串来说，value为字符数
min:value | 字段值必须 >= value，对于字符串来说，value为字符数
email | 验证邮箱是否合法
url | 验证字段是否是有效的 URL 格式
confirmed | 验证连个字段是否相同，如果验证的字段是password，则必须输入一个与之匹配的password_confirmation字段
integer | 验证字段必须是整型
ip | 验证字段必须是 IP 地址
numeric | 字段必须是数值
size:value | 验证字符必须和给定值value相匹配，对字符串而言，value是相应的字符数目；对数值而言，value是给定整型值；对文件而言，value是相应的文件字节数
string | 验证字段必须是字符串
unique | 唯一，字段，需要排除的ID
between:min,max | 验证字段值的大小是否介于指定的min和max之间。字符串、数值或是文件大小的计算方式和size规则相同

## 表单的CSRF验证

表单验证前，我们要对表单的CSRF验证进行处理：

	//只生成token值没有html代码，适用于 ajax提交
	{{ csrf_token }} 

	//生成token的同时还生成了html代码 适用于一般的表单提交
	{{ csrf_field() }} 或 {{ @csrf }} ( >=5.6版本写法 )  

![表单TOKEN](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/yz-1.jpg)

## $this->validate() 验证

$this->validate() 验证比较常用，一般用在控制器中：

	class LoginController extends Controller
	{
	    // 登录处理
	    public function login(Request $request)
	    {
	        //后台表单验证
	        $this->validate($request,[
	            //表单的字段名 => 规则名 多个规则用 | 分开
	            'username' => 'required',
	            'password' => 'required|min:6'
	        ],[
	            //自定义错误提示消息
	            //表单字段名.规则名  =>  错误提示
	            'username.required' => '用户名必须填写',
	            'password.required' => '密码必须填写',
	            'password.min'      => '密码不小于6位'
	        ]);
			dump($request->all());
	    }
	
	}

## 独立方式验证

此方法一般用在模型中

	<?php

	namespace App\Http\Controllers;
	
	use Illuminate\Http\Request;
	//引入类
	use Validator;

	class LoginController extends Controller
	{
	    // 登录处理
	    public function login(Request $request)
	    {
	        //后台表单验证
			//返回的是validate对象
	        $validate = Validator::make($request->all(),[
	            //表单的字段名 => 规则名 多个规则用 | 分开
	            'username' => 'required',
	            'password' => 'required|min:6'
	        ],[
	            //自定义错误提示消息
	            //表单字段名.规则名 => 错误提示
	            'username.required' => '用户名必须填写',
	            'password.required' => '密码必须填写',
	            'password.min'      => '密码不小于6位'
	        ]);
			//如果有错就返回true
			if($validate->fails()){
				return redirect()->back()->withErrors($validate);
			}
			dump($request->all());
			
	    }
	
	}

## 验证器方式验证【推荐】

1. 使用 artisan make:request 验证器名称（UserRequest）命令创建验证器类
2. 在验证器中将授权改为true,写入验证规则和错误提示信息
3. 在控制器中用依赖的注入方式使用

如下所示：

	<?php

	namespace App\Http\Requests;
	
	use Illuminate\Foundation\Http\FormRequest;
	
	class UserRequest extends FormRequest
	{
	    public function authorize()
	    {
	        //return false;
	        return true; // 将授权改为true
	    }
	    public function rules()
	    {
	        return [
	            //表单的字段名 => 规则名 多个规则用 | 分开
	            'username' => 'required',
	            'password' => 'required|min:6'
	        ];
	    }
	
	    //自定义错误提示消息
	    public function messages()
	    {
	        return [
	            //表单字段名.规则名 => 错误提示
	            'username.required' => '用户名必须填写',
	            'password.required' => '密码必须填写',
	            'password.min'      => '密码不小于6位'
			];
	    }
	}

在控制器中引入自定义的验证器类：

	//引入自定义的验证器类
	use App\Http\Requests\UserRequest;

在控制器中使用：public function login(<font color=red>UserRequest</font> $request){...}

	class LoginController extends Controller
	{
	    // 登录处理
	    public function login(UserRequest $request)
	    {
	        dump($request->all());
	    }
	
	}

## 模板中输出错误信息

	<!-- /resources/views/post/create.blade.php -->
	
	<h1>创建文章</h1>
	@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
	@endif
	
	<!-- 创建文章表单 -->

## 错误提示换成中文

1. 在packagist网站：https://packagist.org搜索lang
2. 选择：caouecs/laravel-lang(Languages for Laravel)
3. 进入项目的gitHub地址
4. 下载src目录中的zh-CN目录
5. 将zh-CN目录放在Laravel的resources\lang目录下
6. 将config\app.php中的 'locale' => 'en' 改为 'locale' => 'zh-CN' 

# 数据库

## Laravel支持的数据库类型

Laravel 能使用原生 SQL、查询构造器 和 Eloquent ORM 在各种数据库后台与数据库进行非常简单的交互。当前 Laravel 支持四种数据库:

- MySQL
- Postgres
- SQLite
- SQL Server

Laravel操作数据库所使用的扩展是PDO扩展，一定要开启PDO扩展，PHP配置文件中打开PDO扩展：extension=php_pdo_mysql.dll

## 配置连接数据库信息

在Laravel中修改连接数据库的文件有两处：

- 修改 .env 文件【推荐】（ 好处：方便调控部署，线上线下不需要切换php代码 ）
- 修改 config/database.php 文件


<font color=red>**提示：**

- Laravel5.4之后对数据库的编码就进行了默认改变，默认使用utf8mb4，MySQL版本最好 >= 5.6
- 创建数据库时字符集选用：utf8mb4--UTF-8 Unicode，排序规则：utf8mb4_unicode_ci

</font>

修改 .env 文件来接数据库：

	DB_CONNECTION=mysql
	DB_HOST=主机地址
	DB_PORT=端口
	DB_DATABASE=数据库名
	DB_USERNAME=用户名
	DB_PASSWORD=密码

配置后发现，如果数据表有前缀的话，在 .env 文件中不能去设置，解决办法：

- 在config/databse.php文件中修改'connections' 数组中的 'mysql'数组中修改: 'prefix' => env('DB_PREFIX', ''),
- 在 .env 文件中连接数据库的位置加上：DB_PREFIX=表前缀

config/databse.php文件中：

	'connections' => [
		...,
        'mysql' => [
            'driver' => 'mysql',
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            //给 .env 文件中添加表前缀
            'prefix' => env('DB_PREFIX', ''),
            //严格模式
            'strict' => true,
            'engine' => null,
        ],
		...
	]

.env 文件中：
	
	...
	DB_CONNECTION=mysql
	DB_HOST=主机地址
	DB_PORT=端口
	DB_DATABASE=数据库名
	DB_USERNAME=用户名
	DB_PASSWORD=密码
	DB_PREFIX=表前缀名_
	...

## 检查数据库配置是否正确

	Route::get('pdo',functon(){
		dump(\DB::connection());
	});

## DB类执行原生SQL语句

<font color=red>注：数据表设计时，表的字段就算没有默认值，也要设一个空字符串。</font>

添加操作，返回 true/false:
	
	$sql = "insert into la_users (username,password) values (:username,:password)";
	//$res = DB::insert($sql,[':username'=>'jack',':password'=>'admin888']);
    $res = DB::insert($sql,['username'=>'jack','password'=>'admin888']);
    dump($res);

修改操作，返回受影响的条数：

	$sql = "update la_users set username=:username where id=:id";
    $res = DB::update($sql,['username'=>'张三','id'=>1]);

查询单条操作，返回一个集合对象：

	$sql = "select * from la_users where id=:id";
    $res = DB::selectOne($sql,['id'=>1]);

查询多条操作，返回一个数组的集合：

	$sql = "select * from la_users where id>:id";
    $res = DB::select($sql,['id'=>1]);

删除操作，返回受影响的条数：

	$sql = "delete from la_users where id=:id";
    $res = DB::delete($sql,['id'=>3]);

## 查询构造器操作数据库

Laravel 的查询构造器使用 PDO 参数绑定来保护您的应用程序免受 SQL 注入攻击。因此没有必要清理作为绑定传递的字符串。

<font color=red>注：使用查询构造器时表名不需要写表前缀。</font>

### 查询

此处只列举了部分查询操作，完整查询方法请参考官方文档

查询全部，所有字段都查询：

	$res = DB::table('users')->get();
		
查询全部，并指定字段：

	//方法一
    $res = DB::table('users')->get(['password']);
    //方法二
    $res = DB::table('users')->select('username','password')->get();
		
对象转为数组：

	$res = DB::table('users')->get()->toArray();
		
where条件查询：

    $res = DB::table('users')->where('id','>=', '2')->get();
	//条件查询：或者
    $res = DB::table('users')->where('id','>=', '3')->orWhere('id','1')->get();

	//where还可以传入数组
    $res = DB::table('users')->where([
        ['id','<>','4'],
        ['id','>','3']
    ])->get();

	//字段的值在指定的数组内
	$res = DB::table('users')->whereIn('id', [1, 2, 3])->get();

	//字段的值在指定的数组内
	$res = DB::table('users')->whereNotIn('id', [1, 2, 3])->get();
		
if条件语句，根据关键词搜索：

	//when中的 $kw 为真，执行匿名函数的内容
    $kw = $request->get('kw');
    $res = DB::table('users')->when($kw,function (Builder $query) use ($kw){
        $query->where('password','like',"%{$kw}%");
    })->get();
		
获取单条数据：

	//没有返回 null
    $res = DB::table('users')->where('id',6)->first();

	//获取某个具体的值
    $res = DB::table('users')->where('id',3)->value('password');

	//获取某列的值
    $res = DB::table('users')->pluck('password');
	//获取某列的值，也可以在返回的集合中指定字段的自定义键名
    $res = DB::table('users')->pluck('password','id');
		
排序：

    $res = DB::table('users')->orderBy('id','desc')->get();

查询总记录数：

    $res = DB::table('users')->count();

分页：

	//offset：起始位置  limt:输出的条数
	$res = DB::table('users')->orderBy('id','desc')->offset(0)->limit(2)->get();

### 添加

查询构造器还提供了 insert 方法用于插入记录到数据库中。 insert 方法接收数组形式的字段名和字段值进行插入操作，返回true或false：

	DB::table('users')->insert(
		['email' => 'john@example.com', 'votes' => 0]
	);

还可以在 insert 中传入一个嵌套数组向表中插入多条记录。每个数组代表要插入表中的行：

	DB::table('users')->insert([
	    ['email' => 'taylor@example.com', 'votes' => 0],
	    ['email' => 'dayle@example.com', 'votes' => 0]
	]);
		
#### 自增 ID

如果数据表有自增 ID，使用 insertGetId 方法来插入记录并返回 ID 值：

	$id = DB::table('users')->insertGetId(
	    ['email' => 'john@example.com', 'votes' => 0]
	);

### 更新

查询构造器也可通过 update 方法更新已有的记录，返回受影响的行数。 update 方法和 insert 方法一样，接受包含要更新的字段及值的数组。 你可以通过 where 子句对 update 查询进行约束：

	DB::table('users')
            ->where('id', 1)
            ->update(['votes' => 1]);

#### 更新 JSON 字段

更新 JSON 字段时，你可以使用 -> 语法访问 JSON 对象上相应的值，该操作只能用于支持 JSON 字段类型的数据库：

	DB::table('users')
            ->where('id', 1)
            ->update(['options->enabled' => true]);

#### 自增与自减

查询构造器还为给定字段的递增或递减提供了方便的方法。 此方法提供了一个比手动编写 update 语句更具表达力且更精练的接口。

这两个方法都至少接收一个参数：需要修改的列。第二个参数是可选的，用于控制递增或递减的量。

	DB::table('users')->increment('votes');
	
	DB::table('users')->increment('votes', 5);
	
	DB::table('users')->decrement('votes');
	
	DB::table('users')->decrement('votes', 5);

也可以在操作过程中指定要更新的字段：

	DB::table('users')->increment('votes', 1, ['name' => 'John']);

### 删除

查询构造器也可以使用 delete 方法从数据表中删除记录。在使用 delete 前，可添加 where 子句来约束 delete 语法：

	DB::table('users')->delete();
	
	DB::table('users')->where('votes', '>', 100)->delete();
	
	//简写，主键名必须是id
	DB::table('users')->delete(10);

如果你需要清空表，你可以使用 truncate 方法，这将删除所有行，并重置自增 ID 为零：

	DB::table('users')->truncate();

#### 悲观锁

查询构造器也包含一些可以帮助你在 select 语法上实现 「悲观锁定」的函数。若想在查询中实现一个「共享锁」，你可以使用 sharedLock 方法。共享锁可防止选中的数据列被篡改，直到事务被提交为止 ：

	DB::table('users')->where('votes', '>', 100)->sharedLock()->get();	

另外，你也可以使用 lockForUpdate 方法。使用「更新」锁可避免行被其它共享锁修改或选取：

	DB::table('users')->where('votes', '>', 100)->lockForUpdate()->get();

## 数据库迁移

迁移就像是数据库的版本控制，允许团队简单轻松的编辑并共享应用的数据库表结构，迁移通常和 Laravel 的 数据库结构生成器配合使用，让你轻松地构建数据库结构。如果你曾经试过让同事手动在数据库结构中添加字段，那么数据库迁移可以让你不再需要做这样的事情。

就是使用php文件编写的代码来进行数据库表结构的创建和修改，可以做到快速部署、永久保存、方便管理数据库。


### 生成与编写迁移

使用 Artisan 命令 make:migration 来创建迁移。

新的迁移位于 <u>database/migrations</u> 目录下。每个迁移文件名都包含时间戳，以便让 Laravel 确认迁移的顺序。

--table 和 --create 选项可用来指定数据表的名称，或是该迁移被执行时是否将创建的新数据表。这些选项需在预生成迁移文件时填入指定的数据表：
	
	php artisn make:migration create_users_table
	
	create_users_table   生成的文件后缀名称

	php artisan make:migration create_users_table --create=test
	
	--create=test   生成表名为 test 的数据表

如果你想要指定生成迁移指定一个自定义输出路径，则可以在运行 make:migration 命令时添加 --path 选项，给定的路径必须是相对于应用程序的基本路径。


### 迁移结构

迁移类通常会包含 2 个方法： up 和 down。 up 方法用于添加新的数据表， 字段或者索引到数据库， 而 down 方法就是 up 方法的反操作，和 up 里的操作相反。

在这 2 个方法中都要用到 Laravel 的 Schema 构建器来创建和修改表，
若要了解 Schema 生成器中的所有可用方法 ，[可以查看它的文档](https://learnku.com/docs/laravel/5.6/migrations/1400#creating-tables "可以查看它的文档")。比如，创建 flights 表的简单示例:

	<?php

	use Illuminate\Support\Facades\Schema;
	use Illuminate\Database\Schema\Blueprint;
	use Illuminate\Database\Migrations\Migration;
	
	class CreateFlightsTable extends Migration
	{
	    /**
	     * 运行数据库迁移
	     *
	     * @return void
	     */
	    public function up()
	    {
	        Schema::create('flights', function (Blueprint $table) {
	            $table->increments('id');
	            $table->string('name');
	            $table->string('airline');
	            $table->timestamps();
	        });

			Schema::create('article', function (Blueprint $table) {
	            $table->increments('id');
	            $table->string('title',200)->default('')->comment('标题');
	            $table->unsignedInteger('uid')->default(0)->comment('用户ID');
	            $table->text('cent')->comment('文章内容');
	            $table->timestamps();
	        });
	    }
	
	    /**
	     * 回滚数据库迁移
	     *
	     * @return void
	     */
	    public function down()
	    {
	        //Schema::drop('flights');
			// 如果表存在，就删除
			Schema::dropIfExists('password_resets');
	    }
	}

### 执行数据迁移

使用 Artisan 命令 migrate 方法来运行所有未完成的迁移：

	php artisan migrate

#### 在生产环境强制执行迁移

一些迁移操作是具有破坏性的， 这意味着可能会导致数据丢失。 为了防止有人在生产环境中运行这些命令， 系统会在这些命令被运行之前与你进行确认。如果要强制忽略系统的提示运行命令， 则可以使用 --force 标记：

	php artisan migrate --force

### 回滚迁移

若要回滚最后一次迁移， 可以使用 rollback 命令。 此命令将回滚最后一次 “迁移” 的操作，其中可能包含多个迁移文件：

	php artisan migrate:rollback

可以在 rollback 命令后面加上 step 参数，来限制回滚迁移的个数。 例如，以下命令将回滚最近五次迁移：

	php artisan migrate:rollback --step=5

migrate:reset 命令可以回滚应用程序中的所有迁移：
	
	php artisan migrate:reset

#### 使用单个命令来执行回滚或迁移

migrate:refresh 命令不仅会回滚数据库的所有迁移还会接着运行 migrate 命令。 这个命令可以高效地重建整个数据库：

	php artisan migrate:refresh 

	// 刷新数据库结构并执行数据填充
	php artisan migrate:refresh --seed  //此方法必须在DatabaseSeeder文件中定义好 call

使用 refresh 命令并提供 step 参数来回滚并再执行最后指定的迁移数。例如， 以下命令将回滚并重新执行最后五次迁移：

	php artisan migrate:refresh --step=5

#### 删除所有表 & 迁移

migrate:fresh 命令会从数据库中删除所有表，然后执行 migrate 命令:

	php artisan migrate:fresh

	php artisan migrate:fresh --seed

### 数据表

#### 创建数据表

可以使用 Schema facade 的 create 方法来创建新的数据库表。 create 方法接受两个参数：第一个参数为数据表的名称，第二个参数是 Closure ，此闭包会接收一个用于定义新数据表的 Blueprint 对象：

	Schema::create('users', function (Blueprint $table) {
	    $table->increments('id');
	});

#### 检查数据表 / 字段是否存在

可以使用 hasTable 和 hasColumn 方法来检查数据表或字段是否存在：

	if (Schema::hasTable('users')) {
	    //
	}
	
	if (Schema::hasColumn('users', 'email')) {
	    //
	}

#### 数据库连接 & 表选项

如果要对非默认连接的数据库连接执行结构操作，可以使用 connection 方法：

	Schema::connection('foo')->create('users', function (Blueprint $table) {
	    $table->increments('id');
	});
你可以在数据库结构生成器上使用以下命令来定义表的选项：

命令 | 描述
:-|:-
$table->engine = 'InnoDB'; | 指定表存储引擎 (MySQL)。
$table->charset = 'utf8'; | 指定数据表的默认字符集 (MySQL)。
$table->collation = 'utf8_unicode_ci'; | 指定数据表默认的排序规则 (MySQL)。
$table->temporary(); | 创建临时表 (不支持 SQL Server)。

#### 重命名 / 删除数据表

若要重命名数据表，可以使用 rename 方法：

	Schema::rename($from, $to);

删除数据表， 可使用 drop 或 dropIfExists 方法：

	Schema::drop('users');
	
	Schema::dropIfExists('users');

#### 重命名带外键的数据表

在重命名表之前，你应该验证表上的任何外键约束在迁移文件中都有明确的名称，而不是让 Laravel 按照约定来设置一个名称。否则，外键的约束名称将引用旧表名。


#### 创建字段

使用 Schema facade 的 table 方法可以更新现有的数据表。如同 create 方法一样，table 方法会接受两个参数：一个是数据表的名称，另一个则是接收可以用来向表中添加字段的 Blueprint 实例的闭包：

	Schema::table('users', function (Blueprint $table) {
	    $table->string('email');
	});

#### 可用的字段类型

数据库结构生成器包含构建表时可以指定的各种字段类型：

Command | Description
:-|:-
$table->bigIncrements('id'); | 递增 ID（主键），相当于「UNSIGNED BIG INTEGER」
$table->bigInteger('votes'); | 相当于 BIGINT
$table->binary('data'); | 相当于 BLOB
$table->boolean('confirm ed'); | 相当于 BOOLEAN
$table->char('name', 100); | 相当于带有长度的 CHAR
$table->date('created_at'); | 相当于 DATE
$table->dateTime('created_at'); | 相当于 DATETIME
$table->dateTimeTz('created_at'); | 相当于带时区 DATETIME
$table->decimal('amount', 8, 2); | 相当于带有精度与基数 DECIMAL
$table->double('amount', 8, 2); | 相当于带有精度与基数 DOUBLE
$table->enum('level', ['easy', 'hard']); | 相当于 ENUM
$table->float('amount', 8, 2); | 相当于带有精度与基数 FLOAT
$table->geometry('positions'); | 相当于 GEOMETRY
$table->geometryCollection('positions');| 相当于 GEOMETRYCOLLECTION
$table->increments('id'); | 递增的 ID (主键)，相当于「UNSIGNED INTEGER」
$table->integer('votes'); | 相当于 INTEGER
$table->ipAddress('visitor'); | 相当于 IP 地址
$table->json('options'); | 相当于 JSON
$table->jsonb('options'); | 相当于 JSONB
$table->lineString('positions'); | 相当于 LINESTRING
$table->longText('description'); | 相当于 LONGTEXT
$table->macAddress('device'); | 相当于 MAC 地址
$table->mediumIncrements('id'); | 递增 ID (主键) ，相当于「UNSIGNED MEDIUM INTEGER」
$table->mediumInteger('votes'); | 相当于 MEDIUMINT
$table->mediumText('description'); | 相当于 MEDIUMTEXT
$table->morphs('taggable'); | 相当于加入递增的 taggable_id 与字符串 taggable_type
$table->multiLineString('positions'); | 相当于 MULTILINESTRING
$table->multiPoint('positions'); | 相当于 MULTIPOINT
$table->multiPolygon('positions'); | 相当于 MULTIPOLYGON
$table->nullableMorphs('taggable'); | 相当于可空版本的 morphs() 字段
$table->nullableTimestamps(); | 相当于可空版本的 timestamps() 字段
$table->point('position'); | 相当于 POINT
$table->polygon('positions'); | 相当于 POLYGON
$table->rememberToken(); | 相当于可空版本的 VARCHAR (100) 的 remember_token 字段
$table->smallIncrements('id'); | 递增 ID (主键) ，相当于「UNSIGNED SMALL INTEGER」
$table->smallInteger('votes'); | 相当于 SMALLINT
$table->softDeletes(); | 相当于为软删除添加一个可空的 deleted_at 字段
$table->softDeletesTz(); | 相当于为软删除添加一个可空的 带时区的 deleted_at 字段
$table->string('name', 100); | 相当于带长度的 VARCHAR
$table->text('description'); | 相当于 TEXT
$table->time('sunrise'); | 相当于 TIME
$table->timeTz('sunrise'); | 相当于带时区的 TIME
$table->timestamp('added_on'); | 相当于 TIMESTAMP
$table->timestampTz('added_on'); | 相当于带时区的 TIMESTAMP
$table->timestamps(); | 相当于可空的 created_at 和 updated_at TIMESTAMP
$table->timestampsTz(); | 相当于可空且带时区的 created_at 和 updated_at TIMESTAMP
$table->tinyIncrements('id'); | 相当于自动递增 UNSIGNED TINYINT
$table->tinyInteger('votes'); | 相当于 TINYINT
$table->unsignedBigInteger('votes'); | 相当于 Unsigned BIGINT
$table->unsignedDecimal('amount', 8, 2); | 相当于带有精度和基数的 UNSIGNED DECIMAL
$table->unsignedInteger('votes'); | 相当于 Unsigned INT
$table->unsignedMediumInteger('votes'); | 相当于 Unsigned MEDIUMINT
$table->unsignedSmallInteger('votes'); | 相当于 Unsigned SMALLINT
$table->unsignedTinyInteger('votes'); | 相当于 Unsigned TINYINT
$table->uuid('id'); | 相当于 UUID
$table->year('birth_year'); | 相当于 YEAR

#### 字段修饰

除了上述列出的字段类型之外， 还有几个可以在添加字段时使用的"修饰符" 。例如，如果要把字段设置为 "可空"，就使用 nullable 方法：

	Schema::table('users', function (Blueprint $table) {
	    $table->string('email')->nullable();
	});

以下是所有可用的字段修饰符的列表。此列表不包括 索引修饰符：

Modifier | Description
:-|:-
->after('column') | 将此字段放置在其它字段 "之后" (MySQL)
    ->autoIncrement() | 将 INTEGER 类型的字段设置为自动递增的主键
->charset('utf8') | 指定一个字符集 (MySQL)
    ->collation('utf8_unicode_ci') | 指定列的排序规则 (MySQL/SQL Server)
->comment('my comment') | 为字段增加注释 (MySQL)
    ->default($value) | 为字段指定 "默认" 值
->first() | 将此字段放置在数据表的 "首位" (MySQL)
    ->nullable($value = true) | 此字段允许写入 NULL 值（默认情况下）
->storedAs($expression) | 创建一个存储生成的字段 (MySQL)
    ->unsigned() | 设置 INTEGER 类型的字段为 UNSIGNED (MySQL)
    ->useCurrent() | 将 TIMESTAMP 类型的字段设置为使用 CURRENT_TIMESTAMP 作为默认值
->virtualAs($expression) | 创建一个虚拟生成的字段 (MySQL)

#### 修改字段

**先决条件**

在修改字段之前，请确保将 doctrine/dbal 依赖添加到 composer.json 文件中。Doctrine DBAL 库用于确定字段的当前状态， 并创建对该字段进行指定调整所需的 SQL 查询：

	composer require doctrine/dbal

#### 更新字段属性

change 方法可以将现有的字段类型修改为新的类型或修改属性。 比如，你可能想增加字符串字段的长度，可以使用 change 方法把 name 字段的长度从 25 增加到 50：

	Schema::table('users', function (Blueprint $table) {
	    $table->string('name', 50)->change();
	});

<font color=red>**注：** 只有下面的字段类型能被 "修改"： bigInteger、 binary、 boolean、date、dateTime、dateTimeTz、decimal、integer、json、 longText、mediumText、smallInteger、string、text、time、 unsignedBigInteger、unsignedInteger and unsignedSmallInteger。</font>

#### 重命名字段

可以使用结构生成器上的 renameColumn 方法来重命名字段。在重命名字段前， 请确保你的 composer.json 文件内已经加入 doctrine/dbal 依赖：

	Schema::table('users', function (Blueprint $table) {
	    $table->renameColumn('from', 'to');
	});

<font color=red>**注：**当前不支持 enum 类型的字段重命名。</font>

#### 删除字段

可以使用结构生成器上的 dropColumn 方法来删除字段。 在从 SQLite 数据库删除字段前，你需要在 composer.json 文件中加入 doctrine/dbal 依赖并在终端执行 composer update 来安装该依赖：

	Schema::table('users', function (Blueprint $table) {
	    $table->dropColumn('votes');
	});

你可以传递一个字段数组给 dropColumn 方法来删除多个字段：

	Schema::table('users', function (Blueprint $table) {
	    $table->dropColumn(['votes', 'avatar', 'location']);
	});

<font color=red>**注：**不支持在使用 SQLite 数据库时在单个迁移中删除或修改多个字段。</font>

#### 可用的命令别名

每个索引方法都可以接收一个可选的第二个参数来指定索引的名称。 如果省略，名称将从表和列的名称派生。

Command | Description
:-|:-
$table->dropRememberToken(); | 删除 remember_token 字段。
$table->dropSoftDeletes(); | 删除 deleted_at 字段。
$table->dropSoftDeletesTz(); | dropSoftDeletes() 方法的别名
$table->dropTimestamps(); | 删除 created_at 和 updated_at 字段。
$table->dropTimestampsTz(); | dropTimestamps() 方法的别名。

#### 索引 

**创建索引**

结构生成器支持多种类型的索引。首先，先指定字段值唯一，即简单地在字段定义之后链式调用 unique 方法来创建索引，例如：

	$table->string('email')->unique();

或者，你也可以在定义完字段之后创建索引。例如：

	$table->unique('email');

你甚至可以将数组传递给索引方法来创建一个复合（或合成）索引：

	$table->index(['account_id', 'created_at']);

Laravel 会自动生成一个合理的索引名称，但你也可以传递第二个参数来自定义索引名称：

	$table->unique('email', 'unique_email');

#### 可用的索引类型

命令 | 描述
:-|:-
$table->primary('id'); | 添加主键
$table->primary(['id', 'parent_id']); | 添加复合键
$table->unique('email'); | 添加唯一索引
$table->index('state'); | 添加普通索引
$table->spatialIndex('location'); | 添加空间索引 (SQLite 除外)

#### 索引长度 & MySQL / MariaDB

Laravel 默认使用 utf8mb4 字符，它支持在数据库中存储 "emojis" 。 如果你是在版本低于 5.7.7 的 MySQL release 或者版本低于 10.2.2 的 MariaDB release 上创建索引，那就需要你手动配置迁移生成的默认字符串长度。 即在 AppServiceProvider 中调用 Schema::defaultStringLength 方法来配置它 ：

	use Illuminate\Support\Facades\Schema;
	
	/**
	 * 引导任何应用程序服务。
	 *
	 * @return void
	 */
	public function boot()
	{
	    Schema::defaultStringLength(191);
	}

或者，你可以开启数据库的 innodb_large_prefix 选项。 至于如何正确开启，请自行查阅数据库文档。

#### 删除索引

若要移除索引， 则必须指定索引的名称。Laravel 默认会自动给索引分配合理的名称。其将数据表名称、索引的字段名称及索引类型简单地连接在了一起。举例如下：

命令 | 描述
:-|:-
$table->dropPrimary('users_id_primary'); | 从 "users" 表中删除主键
$table->dropUnique('users_email_unique'); | 从 "users" 表中删除唯一索引
$table->dropIndex('geo_state_index'); | 从 "geo" 表中删除基本索引
$table->dropSpatialIndex('geo_location_spatialindex'); | 从 "geo" 表中删除空间索引 (SQLite 除外)

如果将字段数组传递给 dropIndex 方法，会删除根据表名、字段和键类型生成的索引名称：

	Schema::table('geo', function (Blueprint $table) {
	    $table->dropIndex(['state']); // 删除索引 'geo_state_index'
	});

#### 外键约束

Laravel 还支持创建用于在数据库层中的强制引用完整性的外键约束。 例如，让我们在 posts 表上定义一个引用 users 表的 id 字段的 user_id 字段：

	Schema::table('posts', function (Blueprint $table) {
	    $table->integer('user_id')->unsigned();
	
	    $table->foreign('user_id')->references('id')->on('users');
	});

还可以为约束的 "on delete" 和 "on update" 属性指定所需的操作：

	$table->foreign('user_id')
	      ->references('id')->on('users')
	      ->onDelete('cascade');

你可以使用 dropForeign 方法删除外键。外键约束采用的命名方式与索引相同。即，将数据表名称和约束的字段连接起来， 接着加上 "_foreign" 后缀：

	$table->dropForeign('posts_user_id_foreign');

或者，你也可以传递一个字段数组，在删除的时候会按照约定自动转换为对应的外键名称：

	$table->dropForeign(['user_id']);

你可以在迁移文件里使用以下方法来开启或关闭外键约束：
	
	Schema::enableForeignKeyConstraints();
	
	Schema::disableForeignKeyConstraints();

## 数据填充

Laravel 可以用 seed 类轻松地为数据库填充测试数据。所有的 seed 类都存放在 <u>database/seeds</u> 目录下。你可以任意为 seed 类命名，但是更应该遵守类似 UsersTableSeeder 的命名规范。Laravel 默认定义的一个 DatabaseSeeder 类。可以在这个类中使用 call 方法来运行其它的 seed 类从而控制数据填充的顺序。

数据填充就是往数据库中写测试数据的操作。

### 编写 Seeders

运行 Artisan 命令 make:seeder 生成 Seeder。由框架生成的 seeders 都将被放置在 database/seeds 目录下：

	php artisan make:seeder UsersTableSeeder（种子文件名）

一个 seeder 类只包含一个默认方法：run。这个方法会在 Artisan 命令 db:seed 被执行时调用。在 run 方法里你可以根据需要在数据库中插入数据。你也可以用查询构造器 或 Eloquent 模型工场 来手动插入数据。

<font color=red>**注：**在数据填充期间，批量赋值保护 会自动禁用。</font>

如下所示，在默认的 DatabaseSeeder 类中的 run 方法中添加一条数据插入语句：

	<?php
	
	use Illuminate\Database\Seeder;
	use Illuminate\Support\Facades\DB;
	
	class DatabaseSeeder extends Seeder
	{
	    /**
	     * 运行数据库填充。
	     *
	     * @return void
	     */
	    public function run()
	    {
	        $data = [];
	        for($i=1;$i<=20;$i++){
	            $data[]=[
	                'uid'=>$i,
	                'title'=>'标题:'.$i,
	                'cent'=>'这是内容:'.$i,
	                'password'=> 'admin'.$i.$i.$i
	            ];
	        }
	        DB::table('article')->insert($data);
		 }
	}

### 使用模型工厂

手动为每个模型填充指定属性很麻烦。作为替代方案，你可以使用 [model 工厂](https://learnku.com/docs/laravel/5.6/database-testing#writing-factories "model 工厂") 轻松地生成大量数据库数据。首先，阅读 [model 工厂文档](https://learnku.com/docs/laravel/5.6/database-testing#writing-factories "model 工厂文档") 来学习如何使用工厂，然后就可以使用 factory 这个辅助函数来向数据库中插入数据。

例如，创建 50 个用户并为每个用户创建关联：

	/**
	 * 运行数据库填充。
	 *
	 * @return void
	 */
	public function run()
	{
	    factory(App\User::class, 50)->create()->each(function ($u) {
	        $u->posts()->save(factory(App\Post::class)->make());
	    });
	}

### 调用其它 Seeders

在 DatabaseSeeder 类中，你可以使用 call 方法来运行其它的 seed 类。使用 call 方法可以将数据填充拆分成多个文件，这样就不会使单个 seeder 变得非常大。只需简单传递要运行的 seeder 类名称即可：

	/**
	 * 运行数据库 seeds。
	 *
	 * @return void
	 */
	public function run()
	{
	    $this->call([
	        UsersTableSeeder::class,
	        PostsTableSeeder::class,
	        CommentsTableSeeder::class,
	    ]);
	}

### 运行 Seeders

完成 seeder 类的编写之后，你可能需要使用 dump-autoload 命令重新生成 Composer 的自动加载器：

	composer dump-autoload

接着就可以使用 Artisan 命令 db:seed 来填充数据库了。默认情况下，db:seed 命令将运行 DatabaseSeeder 类，这个类可以用来调用其它 Seed 类。不过，你也可以使用 --class 选项来指定一个特定的 seeder 类：

	php artisan db:seed

	php artisan db:seed --class=UsersTableSeeder

你也可以使用 migrate:refresh 命令来填充数据库，该命令会回滚并重新运行所有迁移。这个命令可以用来重建数据库：

	php artisan migrate:refresh --seed

# 模型

Laravel 的 Eloquent ORM 提供了漂亮、简洁的 ActiveRecord 实现来和数据库交互。每个数据库表都有一个对应的「模型」用来与该表交互。你可以通过模型查询数据表中的数据，并将新记录添加到数据表中。

在开始之前，请确保在 config/database.php 中配置数据库连接。

## 模型文件位置

默认情况下Laravel模型在app目录的根目录下，但这种情况不利于我们日后的项目维护，所以一般建议手动创建一个 Models 目录，这样更加方便管理和维护。

## 定义模型

首先，创建一个 Eloquent 模型，生成的模型通常放在 app 目录中，但你可以通过 composer.json 文件随意地将它们放在可被自动加载的地方。所有的 Eloquent 模型都继承了 Illuminate\Database\Eloquent\Model 类。

创建模型实例的最简单方法是使用 Artisan 命令 make:model：

	php artisan make:model User(模型文件名)

分目录的：

	php artisan make:model Models/User (目录名/模型文件名)

<font color=red>实际工作中，就是创建模型的同时就创建好了迁移文件：可以使用 --migration 或 -m 选项：</font>

	php artisan make:model User --migration
	
	php artisan make:model User -m

## 模型限制

### 定义表名

模型所对应的默认表名是模型类名的复数形式，如果表名不统一则需要制定一下：

	//指定表名，不需加表前缀
	protected $table = 'article';

### 定义主键

Eloquent 也会假定每个数据表都有一个名为 id 的主键字段。你可以定义一个访问权限为 protected 的 $primaryKey 属性来覆盖这个约定。

另外，Eloquent 假定主键是一个递增的整数值，这意味着在默认情况下主键会自动转换为 int 。如果希望使用非递增或者非数字的主键，则必须在模型上设置 public $incrementing = false 。如果主键不是一个整数，你应该在模型上设置 protected $keyType = string 。

	//指定主键
    protected $primaryKey = 'mid';
	
	//使用非递增或者非数字的主键
	public $incrementing = false 

	//如果主键不是一个整数
	protected $keyType = string 

### 时间戳

默认情况下，Eloquent 会默认数据表中存在 created_at 和 updated_at 这两个字段。如果你不需要这两个字段，则需要在模型内将 $timestamps 属性设置为 false ：

	//如果没有created_at 和 updated_at 这两个字段，则需要制定模型不用去管理
	public $timestamps = false;
	
如果你需要自定义时间戳格式，可在模型内设置 $dateFormat 属性。这个属性决定了日期属性应如何存储在数据库中，以及模型被序列化成数组或 JSON 时的格式：

	//定义模型的日期字段的存储格式
	protected $dateFormat = 'U';

如果你需要自定义用于存储时间戳的字段名，可以在模型中通过设置 CREATED_AT 和 UPDATED_AT 常量来实现：

	//自定义用于存储时间戳的字段名
	const CREATED_AT = 'creation_date';
    const UPDATED_AT = 'last_update';

### 数据库连接

默认情况下，所有的模型使用应用配置中的默认数据库连接。如果你想要为模型指定不同的连接，可以通过 $connection 属性来设置： 

	//自定义数据库连接
	protected $connection = 'connection-name';

### 批量赋值（$fillable 与 $guarded）

当通过 <font color=red>**create**</font> 方法来保存数据的时候，你需要先在模型中定义 <font color=red>$fillable</font> 与 <font color=red>$guarded</font> 属性：
	
	//批量赋值
	# 白名单
	protected $fillable = ['允许谈加的字段名'];
	# 黑名单
	protected $guarded = ['不允许谈加的字段名'];
	# 允许所有字段都可以添加
	protected $guarded = [];

## CURD操作
	
### 添加数据

#### save()方法

	//save()方法添加数据【不推荐】
	$model = new Article();
	$model->uid = 1;
	$model->title = '这是标题1';
	$model->cent = '这是save()添加的数据';
	$model->save();
	//返回模型对象
	dump($model->id);

#### insert()方法

<font color=red>**缺点：不能自动填充时间戳字段：**</font>

	//不能自动填充时间戳字段
	# 添加一条
	$data = [
	    'uid'   => '3',
	    'title' => '这是标题 2',
	    'cent'  => '这是insert()添加的数据',
	];
	# 添加多条
	$data2 = [
	    [
	        'uid'   => '3',
	        'title' => '这是标题 2',
	        'cent'  => '这是insert()添加的数据',
	    ],
	    [
	        'uid'   => '4',
	        'title' => '这是标题 3',
	        'cent'  => '这是insert()添加的数据',
	    ]
	];
	//返回布尔类型
	dump(Article::insert($data2));

#### create()方法

<font color=red>**注意：该方法一定要设置 $fillable 或 $guarded 即白名单或黑名单，否则会报错！**</font>

	$data3 = [
	            'uid'   => '5',
	            'title' => '这是标题 4',
	            'cent'  => '这是create()添加的数据',
	        ];
	//返回模型对象
	 dump(Article::create($data3));

### 查询数据

#### 查询单条

	// 查询单条
	$res = Article::where('id',2)->first();

	// 通过主键或者模型中定义的主键取回一条数据
	$res = Article::find(3);

	// 使用主键数组作为参数调用 find 方法，它将返回匹配记录的集合，
	$res = Article::find([1,2,3]);

	// 返回模型对象，可使用 toArray() 转为数组
	dump($res);

#### 查询多条
	
	// 不能使用条件，返回模型对象
	$res = Article::all();
	
	// 可以跟随条件，返回模型对象
	$res = Article::get();
	$res = Article::where('id','>','4')->get();

	// 使用主键数组作为参数调用 find 方法，它将返回匹配记录的集合
	$res = Article::find([1,2,3]);

	dump($res);

#### 查询指定字段值
	
	$res = Article::where('id',3)->value('title');

#### 查询一列
	
	// 返回集合对象
	$res = Article::where('id',3)->pluck('title');
	// 使用id的值作为键名
	$res = Article::where('id',3)->pluck('title','id);

#### 查询总记录数
	
	// 返回 int 
	$res = Article::count();
	$res = Article::where('id','>',4)->count();

#### 分页
	
	// 返回集合，中有模型对象 
	$res = Article::offset(0)->limit(2)->get();
	// 跟随条件分页
	$res = Article::where('id','>',2)->offset(0)->limit(3)->get();

### 修改数据

#### 方案一

	// 返回模型对象
    $model = Article::find(1);
    $model->title = '这是修改的标题1';
    // 保存
    $model->save();

#### 方案二

	// 返回受影响的行数
    $data = ['title'=> '再次修改2'];
    $model = Article::where('id',2)->update($data);

### 删除数据


#### 真删除

##### delete 方法删除：

	$id = 8;
    $model = Article::find($id);
    $model->delete();

##### 通过主键删除

	Article::destroy(7);
    Article::destroy([6,5]);
    Article::destroy(5,6);
  
##### 通过查询删除

	Article::where('id',6)->delete();

#### 软删除

软删除要满足以下条件：

1. 在表字段中要有 delete_at 字段
2. 在模型中引入 trait  use SoftDelete;
3. 把 deleted_at 字段加入 $dates 属性：

	
		<?php
		
		namespace App;
		
		use Illuminate\Database\Eloquent\Model;
		use Illuminate\Database\Eloquent\SoftDeletes;
		
		class Flight extends Model
		{
		    use SoftDeletes;
		
		    /**
		     * 需要转换成日期的属性
		     *
		     * @var array
		     */
		    protected $dates = ['deleted_at'];
		}


当然，需要把 deleted_at 字段加到数据库表。Laravel 的 表结构构造器 提供了一个辅助方法来生成这个字段：

	Schema::table('flights', function ($table) {
	    $table->softDeletes();
	});

#### 查询软删除

##### 查询所有包括软删除的数据

前边已经提到，查询结果会自动剔除软删除模型。然而，查询中可以使用 withTrashed 方法获取包括软删除在内的模型：

	$res = Article::withTrashed()->get();

withTrashed 方法也可以用在 关联 查询：

	$flight->history()->withTrashed()->get();

##### 只查询软删除的

	$res = Article::onlyTrashed()->get();

#### 恢复软删除

有时会对软删除模型进行「撤销」，在软删除模型实例上使用 restore 方法即可恢复到有效状态：

	$model = Article::onlyTrashed()->first();
    $model->restore();

#### 永久删除

要真实删除数据时，使用 forceDelete 方法即可把软删除模型从数据里永久删除：

	// 单个模型实例的永久删除
	$model = Article::onlyTrashed()->first();
    $model->forceDelete();
	
	// 关联模型的永久删除
	$flight->history()->forceDelete();

## 使用faker进行数据填充

Laravel从5之后就默认已经安装了此扩展包，放在了 <u>vendor/fzaninotto/faker</u> 目录中，[faker 官方地址]('https://packagist.org/packages/fzaninotto/faker')。下面以填充文章表为例的具体步骤：

1. 在 ArticleSeeder.php 种子文件中引入文章模型 `use App\Models\Article; `
2. 实例化 faker `$faker = Faker\Factory::create();`
3. 使用 faker 提供的数据类型填充各字段
4. 调用模型的 create() 方法添加数据
5. 执行 `php artisan migrate:refresh --seed`完成数据填充

在 <u>database/seeds/ArticleSeeder.php</u> 的使用实例：
	
	<?php

	use Illuminate\Database\Seeder;
	//引入文章模型
	use App\Models\Article;
	
	class ArticleSeeder extends Seeder
	{
	    /**
	     * Run the database seeds.
	     *
	     * @return void
	     */
	    public function run()
	    {
	        //清空数据表，id归零
	        Article::truncate();
	        //实例化
	        $faker = Faker\Factory::create();
	        //添加数据
	        for($i=1;$i<=10;$i++){
	            $data=[
	                'uid'=>$i,
	                'title'=>$faker->word,
	                'cent'=>$faker->text
	            ];
	            Article::create($data);
	        }
	
	    }
	}

## 使用faker加数据工厂进行数据填充

**此方法为推荐方法，**其具体步骤如下：

1. 使用命令，在 <u>database/factories/</u> 目录下创建文章表数据工厂文件:`php artisan make:factory ArticleFactory -m Models/Article `
2. 在 ArticleFactory.php 数据工厂文件中的 return 数组中，将各字段使用 faker 的数据格式进行填充
3. 在种子文件 ArticleSeeder.php 中调用数据工厂：`factory(Article::class,20)->create();`
4. 在 DatabaseSeeder.php 种子文件中调用相关的种子文件
5. 执行 `php artisan migrate:refresh --seed`完成数据填充

文章表数据工厂文件：

	<?php
	
	use Faker\Generator as Faker;
	
	$factory->define(App\Models\Article::class, function (Faker $faker) {
	    return [
	        'uid'=> rand(1,10),
	        'title'=>$faker->word,
	        'cent'=>$faker->text
	    ];
	});


文章表种子文件：

	<?php
	
	use Illuminate\Database\Seeder;
	//引入文章模型
	use App\Models\Article;
	
	class ArticleSeeder extends Seeder
	{
	    /**
	     * Run the database seeds.
	     *
	     * @return void
	     */
	    public function run()
	    {
	        // 调用数据工厂
	        //参数1 模型名称   参数2 创建多少条
	        factory(Article::class,20)->create();
	    }
	}


DatabaseSeeder.php文件：

	<?php
	
	use Illuminate\Database\Seeder;
	
	class DatabaseSeeder extends Seeder
	{
	    /**
	     * Seed the application's database.
	     *
	     * @return void
	     */
	    public function run()
	    {
	        $this->call(ArticleSeeder::class);
	    }
	}

## faker本地化操作

### 方法一

在实例化 faker 时指定语言： `$faker = Faker\Factory::create('zh-CN');`

### 方法二【推荐】

在 app.php 中添加 faker全局的本地化：`'locale_faker'=>'zh-CN',`

## 模型事件 

Eloquent 模型会触发许多事件，让你在模型的生命周期的多个时间点进行监控： retrieved, creating, created, updating, updated, saving, saved, deleting, deleted, restoring, restored。 事件让你在每当有特定模型类进行数据库保存或更新时，执行代码。

当从数据库检索现有模型时，会触发 retrieved 事件。当模型第一次被保存时， creating 和 created 事件会被触发。若数据库中已存在此模型，并调用 save 方法，updating / updated 事件会被触发。 这两种情况下，saving / saved 事件都会被触发。

开始前，先在你的 Eloquent 模型上定义一个 $dispatchesEvents 属性，将 Eloquent 模型的生命周期的多个点映射到你的 事件类：

	<?php
	
	namespace App;
	
	use App\Events\UserSaved;
	use App\Events\UserDeleted;
	use Illuminate\Notifications\Notifiable;
	use Illuminate\Foundation\Auth\User as Authenticatable;
	
	class User extends Authenticatable
	{
	    use Notifiable;
	
	    /**
	     * 此模型的事件映射	
	     *
	     * @var array
	     */
	    protected $dispatchesEvents = [
	        'saved' => UserSaved::class,
	        'deleted' => UserDeleted::class,
	    ];
	}

## 观察者

如果你在一个给定模型中监听许多事件，你可以使用观察者将所有的监听添加到一个类。观察者类里的方法名应该反映 Eloquent 想监听的事件。 每个方法接受 model 作为唯一参数。Laravel 不包含观察者的默认目录，所以你可以创建任何你喜欢的目录来存放观察者类：

	<?php

	namespace App\Observers;
	
	use App\User;
	
	class UserObserver
	{
	    /**
	     * 监听创建用户事件.
	     *
	     * @param  \App\User  $user
	     * @return void
	     */
	    public function created(User $user)
	    {
	        //
	    }
	
	    /**
	     * 监听删除用户事件.
	     *
	     * @param  \App\User  $user
	     * @return void
	     */
	    public function deleting(User $user)
	    {
	        //
	    }
	}


要注册一个观察者，需要用模型中的 observe 方法去观察。 你可以在你的服务提供者之一的 boot 方法中注册观察者。在这个例子中，我们将在 AppServiceProvider 中注册观察者：

	<?php
	
	namespace App\Providers;
	
	use App\User;
	use App\Observers\UserObserver;
	use Illuminate\Support\ServiceProvider;
	
	class AppServiceProvider extends ServiceProvider
	{
	    /**
	     * 运行所有应用服务.
	     *
	     * @return void
	     */
	    public function boot()
	    {
	        User::observe(UserObserver::class);
	    }
	
	    /**
	     * 注册服务提供.
	     *
	     * @return void
	     */
	    public function register()
	    {
	        //
	    }
	}

**方案一**

官方方法：在AppServiceProvider中boot方法注册模型对应的观察者。

![事件使用方法一](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/event-1.jpg)

**方案二【推荐】**

在本模型中去注册观察，修改时方便：

![事件使用方法二](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/event-2.jpg)

# 分页

在 Larvel 中要完成分页是很简单的， laravel 是直接调用模型的分页方法，返回对应的数据和分页的字符串。

## 查询构造器分页

对数据进行分页有几种方式。 最简单的是在 查询语句构造器 或者 Eloquent 语句 使用 paginate 方法。 paginate 会根据用户正在浏览的页面自动设置限制和偏移量。 默认情况下，HTTP 请求所带 page 参数的值为当前页码。当然这个值是被 Laravel 自动检测的，同时会自动的插入分页器生成的链接中。

在以下的例子中，传递给 paginate 方法的唯一参数是 「每页」显示的项目数量。下面是每页显示 15 项的例子：

	<?php
	
	namespace App\Http\Controllers;
	
	use Illuminate\Support\Facades\DB;
	use App\Http\Controllers\Controller;
	
	class UserController extends Controller
	{
	    /**
	     * 显示应用中所有的用户。
	     *
	     * @return Response
	     */
	    public function index()
	    {
	        $users = DB::table('users')->paginate(15);
	
	        return view('user.index', ['users' => $users]);
	    }
	}

<font color=red>**提示：**目前，Laravel 无法高效的执行带有 groupBy 语句的分页操作。如果你需要在查询结果中使用 groupBy 语句，建议你查询数据库并手动创建分页器。</font>

### 简单分页

如果你只需要在分页视图中显示简单的「下一页」和「上一页」的链接，即不需要显示每个页码的链接，更推荐使用 simplePaginate 方法来执行更高效的查询，这对于大型数据集非常有用：

	$users = DB::table('users')->simplePaginate(15);

## Eloquent 模型分页

你也可以对 Eloquent 查询进行分页。下面的例子中对 User 模型进行了分页并且每页显示 15 条数据。正如你看到的，所使用的语法几乎与基于查询语句构造器分页时的完全相同：

	$users = App\User::paginate(15);

当然，你可以在设置了查询的其他约束条件之后调用 paginate 方法，例如 where 语句：

	$users = User::where('votes', '>', 100)->paginate(15);

你也可以在 Eloquent 模型中使用 simplePaginate 方法进行分页：

	$users = User::where('votes', '>', 100)->simplePaginate(15);

## 手动创建分页

如果你想手动创建分页实例并且最终得到一个数组类型的结果，可以根据需求来创建 Illuminate\Pagination\Paginator 或者 Illuminate\Pagination\LengthAwarePaginator 实例来实现。

Paginator 类不需要知道结果集中的数据量，因此该类没有检索最后一页索引的方法。而 LengthAwarePaginator 接收的参数几乎和 Paginator 一样，但它却需要计算结果集中的数据量。

说白了就是，Paginator 相当于查询语句构造器和 Eloquent 中的 simplePaginate 方法，而 LengthAwarePaginator 相当于 paginate 方法。

<font color=red>**提示：**手动创建分页器实例时，你应该手动「切割」传递给分页器的结果集数组。如果你不确定如何去做到这一点，请查阅 PHP 的 array_slice 函数。</font>

## 显示分页结果

在调用 paginate 方法时，你将会接收到一个 Illuminate\Pagination\LengthAwarePaginator 实例。当调用 simplePaginate 方法时，你将会接收到一个 Illuminate\Pagination\Paginator 实例。这些对象提供了一些用于渲染结果集的函数。除了这些辅助函数，分页器实例是一个迭代器，也可以作为数组循环。因此，一旦检测到结果集，你可以使用 Blade 模板显示结果集并渲染页面链接：

	<div class="container">
	    @foreach ($users as $user)
	        {{ $user->name }}
	    @endforeach
	</div>

	{{ $users->links() }}

links 方法将会链接渲染到结果集中其余的页面。每个链接都包含了正确的 page 查询字符串变量。记住，links 方法生成的 HTML 与 Bootstrap CSS 框架兼容。

### 自定义分页器的 URI
withPath 方法允许你在生成链接时自定义分页器所使用的 URI。例如，如果你想要分页器生成像 http://example.com/custom/url?page=N 这样的链接，那就传递 custom/url 到 withPath 方法：

	Route::get('users', function () {
	    $users = App\User::paginate(15);
	
	    $users->withPath('custom/url');
	
	    //
	});

### 附加参数到分页链接中

你可以使用 appends 方法将查询参数附加到分页链接中。例如，要将 sort=votes 附加到每个分页链接，可以这样调用 appends 方法：

	{{ $users->appends(['sort' => 'votes'])->links() }}

如果想将「锚点」附加到分页器的链接中，就使用 fragment 方法。例如，要将 #foo 附加到每个分页链接的末尾，应该这样调用 fragment 方法：

	{{ $users->fragment('foo')->links() }}

### 将结果转换为 JSON

Laravel 分页器结果类实现了 Illuminate\Contracts\Support\Jsonable 接口契约并且提供 toJson 方法，因此将分页结果转换为 JSON 非常简单。你也可以在路由或控制器操作中简单地将分页实例转换为 JSON 返回：

	Route::get('users', function () {
	    return App\User::paginate();
	});

分页器中获取的 JSON 将包含元信息，如：total、current_page、last_page 等等。实际的结果对象将通过 JSON 数组中的 data 键来获取。 以下是一个从路由返回的分页器实例创建的 JSON 示例：
	
	{
	   "total": 50,
	   "per_page": 15,
	   "current_page": 1,
	   "last_page": 4,
	   "first_page_url": "http://laravel.app?page=1",
	   "last_page_url": "http://laravel.app?page=4",
	   "next_page_url": "http://laravel.app?page=2",
	   "prev_page_url": null,
	   "path": "http://laravel.app",
	   "from": 1,
	   "to": 15,
	   "data":[
	        {
	            // 结果集
	        },
	        {
	            // 结果集
	        }
	   ]
	}

## 自定义分页视图

默认情况下，渲染分页链接的视图与 Bootstrap CSS 框架兼容。但是，如果你不使用 Bootstrap，可以自由定义视图来渲染这些链接。在分页器实例中调用 links 方法时，只需将视图名称作为第一个参数传递给方法：

	{{ $paginator->links('view.name') }}
	
	// Passing data to the view...
	{{ $paginator->links('view.name', ['foo' => 'bar']) }}

自定义分页视图的制作最简单的方法是使用 vendor:publish 命令将它们导出到 resources/views/vendor 目录：

	php artisan vendor:publish --tag=laravel-pagination

这个命令将这些视图放置在 resources/views/vendor/pagination 目录中。这个目录下的 bootstrap-4.blade.php 文件对应默认的分页视图，你可以简单地编辑这个文件来修改分页的 HTML 。


## 分页器实例方法
每个分页器实例可以通过以下方法获取额外的分页信息：

- $results->count()
- $results->currentPage()
- $results->firstItem()
- $results->hasMorePages()
- $results->lastItem()
- $results->lastPage() （使用 simplePaginate 时不可用）
- $results->nextPageUrl()
- $results->perPage()
- $results->previousPageUrl()
- $results->total() （使用 simplePaginate 时不可用）
- $results->url($page)

# Session

Laravel 中的 session 默认存到文件中

session 文件目录： <u>stroage\framework\sessions</u>

操作 session 的方法在 Laravel 中有2种方案，有类来操作也有利用辅助函数来操作：

- Session 类操作
- 辅助函数操作 session() 

## 配置

Session 的配置文件存储在 config/session.php 文件中。请务必查看此文件中对于你可用的选项。默认情况下，Laravel 配置了适用于大多数应用程序的 file Session 驱动。在生产环境下，你可以考虑使用 memcached 或 redis 驱动来实现更出色的 Session 性能。

Session driver 的配置选项定义了每个请求存储 Session 数据的位置。Laravel 自带了几个不错且可开箱即用的驱动：

- file - 将 Session 存储在 storage/framework/sessions 中。
- cookie - Session 存储在安全加密的 cookie 中。
- database - 将 Session 存储在数据库中。
- memcached / redis - Session 存储在基于高速缓存的存储系统中。
- array - Sessions 存储在 PHP 数组中，但不会被持久化。

<font color=red>**提示:**数组驱动一般用于 测试 ，并防止存储在 Session 中的数据被持久化。</font>

## 驱动程序先决条件

### 数据库

使用 database 作为 Session 驱动时，你需要创建一张包含 Session 各项数据的表。以下例子是使用 Schema 建表：

	Schema::create('sessions', function ($table) {
	    $table->string('id')->unique();
	    $table->unsignedInteger('user_id')->nullable();
	    $table->string('ip_address', 45)->nullable();
	    $table->text('user_agent')->nullable();
	    $table->text('payload');
	    $table->integer('last_activity');
	});

你可以使用 Artisan 命令 session：table 来生成此迁移：

	php artisan session:table

	php artisan migrate

### Redis

Laravel 在使用 Redis 作为 Session 驱动之前，需要通过 Composer 安装 predis/predis 扩展包 (~1.0)。然后在 database 配置文件中配置 Redis 连接信息。在 session 配置文件中，connection 选项可用于指定 Session 使用哪个 Redis 连接。

## 使用 Session

### 获取数据

Laravel 中处理 Session 数据有两种主要方法：全局辅助函数 session 和通过一个 Request 实例。首先，我们来看看通过控制器方法类型提示一个 Request 实例来访问 session。控制器方法依赖项会通过 Laravel 服务容器 实现自动注入:

	<?php
	
	namespace App\Http\Controllers;
	
	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;
	
	class UserController extends Controller
	{
	    /**
	     * 展示给定用户的配置文件。
	     *
	     * @param  Request  $request
	     * @param  int  $id
	     * @return Response
	     */
	    public function show(Request $request, $id)
	    {
	        $value = $request->session()->get('key');
	
	        //
	    }
	}

当你从 Session 获取值时，你还可以传递一个默认值作为 get 方法的第二个参数。如果 Session 中不存在指定的键，便会返回这个默认值。若传递一个闭包作为 get 方法的默认值，并且所请求的键并不存在时，get 方法将执行闭包并返回其结果：

	$value = $request->session()->get('key', 'default');
	
	$value = $request->session()->get('key', function () {
	    return 'default';
	});

#### 全局辅助函数 Session

你也可以使用全局的 PHP 辅助函数 session 来获取和存储的 Session 数据。 使用单个字符串类型的值作为参数调用辅助函数 session 时，它会返回该字该符串对应的 Session 键的值。当使用一个键值对数组作为参数调用辅助函数 session 时，传入的键值将会存储在 Session 中：
	
	Route::get('home', function () {
	    // 获取 session 中的一条数据...
	    $value = session('key');
	
	    // 指定一个默认值...
	    $value = session('key', 'default');
	
	    // 在 Session 中存储一条数据...
	    session(['key' => 'value']);
	});

#### 获取所有的 Session 数据

如果你想要获取所有的 Session 数据，可以使用 all 方法：

	$data = $request->session()->all();

#### 判断 Session 中是否存在某个值

要确定 Session 中是否存在某个值，可以使用 has 方法。如果该值存在且不为 null，那么 has 方法会返回 true：

	if ($request->session()->has('users')) {
	    //
	}

要确定 Session 中是否存在某个值，即使其值为 null，也可以使用 exists 方法。如果值存在，则 exists 方法返回 true：

	if ($request->session()->exists('users')) {
	    //
	}

### 存储数据

要存储数据到 Session，你可以使用 put 方法，或者使用辅助函数 session。

	// 通过请求实例...
	$request->session()->put('key', 'value');
	
	// 通过全局辅助函数...
	session(['key' => 'value']);

#### 在 Session 数组中保存数据

push 方法可以将一个新的值添加到 Session 数组内。例如，假设 user.teams 这个键是包含团队名称的数组，你可以像这样将一个新的值加入到此数组中：

	$request->session()->push('user.teams', 'developers');

#### 检索 & 删除一个项目

pull 方法可以只用一条语句就从 Session 中检索并且删除一个项目：

	$value = $request->session()->pull('key', 'default');

### 闪存数据

有时候你仅想在下一个请求之前在 Session 中存入数据，你可以使用 flash 方法。使用这个方法保存在 Session 中的数据，只会保留到下个 HTTP 请求到来之前，然后就会被删除。闪存数据主要用于短期的状态消息：

	$request->session()->flash('status', 'Task was successful!');

如果需要给更多请求保留闪存数据，可以使用 reflash 方法，这将会所有的闪存数据保留给其它请求。如果只想保留特定的闪存数据，则可以使用 keep 方法：

	$request->session()->reflash();
	
	$request->session()->keep(['username', 'email']);

### 删除数据

forget 方法可以从 Session 内删除一条数据。如果你想删除 Session 内所有数据，可以使用 flush 方法：

	$request->session()->forget('key');
	
	$request->session()->flush();

### 重新生成 Session ID

重新生成 Session ID，通常是为了防止恶意用户利用 session fixation 对应用进行攻击。

如果使用了内置函数 LoginController，Laravel 会自动重新生成身份验证中的 Session ID。否则，你需要手动使用 regenerate 方法重新生成 Session ID。

	$request->session()->regenerate();

## 添加自定义 Session 驱动

### 实现驱动

你自定义的 Session 驱动必须实现 SessionHandlerInterface 接口。这个接口包含了一些我们需要实现的简单方法。下面是一个大概的 MongoDB 实现流程示例：

	<?php
	
	namespace App\Extensions;
	
	class MongoSessionHandler implements \SessionHandlerInterface
	{
	    public function open($savePath, $sessionName) {}
	    public function close() {}
	    public function read($sessionId) {}
	    public function write($sessionId, $data) {}
	    public function destroy($sessionId) {}
	    public function gc($lifetime) {}
	}

<font color=red>**提示:**Laravel 默认没有附带扩展用的目录，你可以把它放在你喜欢的目录内。在下面这个例子中，我们创建了一个 Extensions 目录放置自定义的 MongoHandler 扩展。</font>

接口中的这些方法不太容易理解。让我们来快速了解每个方法的作用：

- open 方法通常用于基于文件的 Session 存储系统。因为 Laravel 已经附带了一个 file 的驱动，所以你不需要在该方法中放置任何代码。PHP 要求必需要有这个方法的实现（这只是一个糟糕的接口设计），你只需要把这个方法置空。
- close 方法跟 open 方法很相似，通常也可以被忽略。对大多数的驱动而言，此方法不是必须的。
- read 方法应当返回与给定的 $sessionId 相匹配的 Session 数据的字符串格式。在你的自定义的驱动中获取或存储 Session 数据时，不需要进行任何序列化或其它编码，因为 Laravel 会执行序列化。
- write 将与 $sessionId 关联的给定的 $data 字符串写入到一些持久化存储系统，如 MongoDB、Dynamo 等。再次重申，你不需要进行任何序列化或其它编码，因为 Laravel 会自动处理这些事情。
- destroy 方法会从持久化存储中移除与 $sessionId 相关联的数据
- gc 方法能销毁给定的 $lifetime （UNIX 的时间戳）之前的所有数据。对本身拥有过期机制的系统如 Memcached 和 Redis 而言，该方法可以置空。

### 注册驱动

你的 Session 驱动实现之后，你还需要在框架中注册该驱动，即将该扩展驱动添加到 Laravel Session 后台。然后在 服务提供者 的 boot 方法内调用 Session Facade 的 extend 方法。之后你就可以从现有的 AppServiceProvider 或者新创建的提供器中执行此操作。

	<?php
	
	namespace App\Providers;
	
	use App\Extensions\MongoSessionHandler;
	use Illuminate\Support\Facades\Session;
	use Illuminate\Support\ServiceProvider;
	
	class SessionServiceProvider extends ServiceProvider
	{
	    /**
	     * 执行注册后引导服务。
	     *
	     * @return void
	     */
	    public function boot()
	    {
	        Session::extend('mongo', function ($app) {
	            // Return implementation of SessionHandlerInterface...
	            return new MongoSessionHandler;
	        });
	    }
	
	    /**
	     * 在容器中注册绑定。
	     *
	     * @return void
	     */
	    public function register()
	    {
	        //
	    }
	}

一旦 Session 驱动被注册，则必须在 config/session.php 的配置文件内使用 Mongo 驱动。

# 中间件

中间件作为请求与响应的中间人，它是一种过滤机制。

Laravel 中间件提供了一种方便的机制来过滤进入应用的 HTTP 请求。例如，Laravel 内置了一个中间件来验证用户的身份认证。如果用户没有通过身份认证，中间件会将用户重定向到登录界面。但是，如果用户被认证，中间件将允许该请求进一步进入该应用。

当然，除了身份认证以外，还可以编写另外的中间件来执行各种任务。例如：CORS 中间件可以负责为所有离开应用的响应添加合适的头部信息；日志中间件可以记录所有传入应用的请求。

Laravel 自带了一些中间件，包括身份验证、CSRF 保护等。所有这些中间件都位于 <u>app/Http/Middleware</u> 目录。

## 定义中间件

通过运行 “make:middleware 中间件名称” 命令来创建新的中间件：

	php artisan make:middleware CheckAge

该命令将会在 app/Http/Middleware 目录下创建一个新的 CheckAge 类，在这个中间件中，我们仅允许 age 参数大于 200 的请求对此路由进行访问，否则，我们将此用户重定向到 home 。


	<?php
	
	namespace App\Http\Middleware;
	
	use Closure;
	
	class CheckAge
	{
	    /**
	     * 处理传入的请求
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @param  \Closure  $next
	     * @return mixed
	     */
	    public function handle($request, Closure $next)
	    {
	        if ($request->age <= 200) {
	            return redirect('home');
	        }
	
	        return $next($request);
	    }
	}

正如你所见，假如给定的 age 参数小于或等于 200 ，这个中间件将返回一个 HTTP 重定向到客户端；否则，请求将进一步传递到应用中。要让请求继续传递到应用程序中（即允许「通过」中间件验证的），只需使用 $request 作为参数去调用回调函数 $next 。

最好将中间件想象为一系列 HTTP 请求必须经过才能进入你应用的「层」。每一层都会检查请求（是否符合某些条件），（如果不符合）甚至可以（在请求访问你的应用之前）完全拒绝掉。

### 前置 & 后置中间件

中间件是在请求之前或之后运行取决于中间件本身。例如，接下来的这个中间件将在应用处理请求 之前 执行其任务：

	<?php
	
	namespace App\Http\Middleware;
	
	use Closure;
	
	class BeforeMiddleware
	{
	    public function handle($request, Closure $next)
	    {
	        // Perform action
	
	        return $next($request);
	    }
	}

而接下来的这个中间件将在应用处理请求 之后 执行其任务：

	<?php
	
	namespace App\Http\Middleware;
	
	use Closure;
	
	class AfterMiddleware
	{
	    public function handle($request, Closure $next)
	    {
	        $response = $next($request);
	
	        // 执行操作
	
	        return $response;
	    }
	}

## 注册中间件

在 Laravel 中有两种类型的中间件，即：**全局中间件**和**路由中间件**

- 全局中间件将在应用程序的每个 HTTP 请求运行而路由中间件将被分配到一个特定的路由。
- 中间件可在 <u>app/Http/kernel.php</u> 中注册，该文件包含两个属性： $middleware 和 $routeMiddleware，$middleware 属性用于注册全局中间件， $routeMiddelware 属性用于注册指定的路由中间件。

### 全局中间件

假设你想让中间件在应用处理每个 HTTP 请求期间运行，只需要在 app/Http/Kernel.php 中的 $middleware 属性中列出这个中间件


### 为路由分配中间件

假设你想为指定的路由分配中间件，首先应该在 app/Http/Kernel.php 文件内为该中间件分配一个 键 。默认情况下， Kernel 类的 $routeMiddleware 属性下包含了 Laravel 内置的中间件。若要加入自定义的中间件，只需把它附加到列表后并为其分配一个自定义 键 即可。例如：

	// 在 App\Http\Kernel 类中
	
	protected $routeMiddleware = [
	    'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
	    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
	    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
	    'can' => \Illuminate\Auth\Middleware\Authorize::class,
	    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
	    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
	];

一旦在 Kernel 类中定义好了中间件，就可以通过 middleware 方法将为路由分配中间件：

	Route::get('admin/profile', function () {
	    //
	})->middleware('auth');

你也可以为路由分配多个中间件：

	Route::get('/', function () {
	    //
	})->middleware('first', 'second');

分配中间件时，你还可以传递完整的类名：

	use App\Http\Middleware\CheckAge;
	
	Route::get('admin/profile', function () {
	    //
	})->middleware(CheckAge::class);


## 中间件组

某些时候你可能希望使用一个 key 把多个中间件打包成一个组，方便将他们应用到路由中。你可以使用 Http kernel 的 $middlewareGroups 属性。

Laravel 内置了 web 和 api 两个中间件组，它们包含了常用的中间件，你可能会想应用到 web UI 和 API 路由中：

	/**
	 * 应用程序的路由中间件组
	 *
	 * @var array
	 */
	protected $middlewareGroups = [
	    'web' => [
	        \App\Http\Middleware\EncryptCookies::class,
	        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
	        \Illuminate\Session\Middleware\StartSession::class,
	        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
	        \App\Http\Middleware\VerifyCsrfToken::class,
	        \Illuminate\Routing\Middleware\SubstituteBindings::class,
	    ],
	
	    'api' => [
	        'throttle:60,1',
	        'auth:api',
	    ],
	];


中间件组和单个中间件一样可以被应用到路由和控制器行为中。同时，中间组很方便得将多个中间件一次性应用到路由上：

	Route::get('/', function () {
	    //
	})->middleware('web');
	
	Route::group(['middleware' => ['web']], function () {
	    Route::get('user','UserController@index')->name('user);
	});

<font color=red>**提示：**RouteServiceProvider 将 web 中间组自动应用到 routes/web.php </font>

**控制器中使用中间件**

	<?php
	
	namespace App\Http\Controllers;
	
	use Illuminate\Http\Request;
	
	class ArticleController extends Controller
	{
	    public function __construct()
	    {
	        return $this->middleware('checkAge');
			// ->except(['index']) 黑名单 写了就不给它进行中间件过滤
			//retruen $this->middleware(['checkAge'])->except(['index']);
	    }
	}

## 中间件参数

中间件也可以接受额外的参数。举个例子，假如你的应用需要在执行特定操作之前验证用户是否为给定的 「角色」，你可以通过创建一个 CheckRole 中间件，由它来接收「角色」名称作为附加参数。

附加的中间件参数应该在 $next 参数之后被传递：

	<?php
	
	namespace App\Http\Middleware;
	
	use Closure;
	
	class CheckRole
	{
	    /**
	     * 处理传入的参数
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @param  \Closure  $next
	     * @param  string  $role
	     * @return mixed
	     */
	    public function handle($request, Closure $next, $role)
	    {
	        if (! $request->user()->hasRole($role)) {
	            // 重定向
	        }
	
	        return $next($request);
    }

定义路由时通过一个 **:** 来隔开中间件名称和参数来指定中间件参数。多个参数就使用逗号分隔：

	Route::put('post/{id}', function ($id) {
	    //
	})->middleware('role:editor');

## Terminable 中间件

有时中间件可能需要在 HTTP 响应发送到浏览器之后处理一些工作。比如，Laravel 内置的「session」中间件会在响应发送到浏览器之后将会话数据写入存储器中。如果你在中间件中定义一个 terminate 方法，则会在响应发送到浏览器后自动调用：

	<?php
	
	namespace Illuminate\Session\Middleware;
	
	use Closure;
	
	class StartSession
	{
	    public function handle($request, Closure $next)
	    {
	        return $next($request);
	    }
	
	    public function terminate($request, $response)
	    {
	        // Store the session data...
	    }
	}

terminate 方法应该同时接收请求和响应。一旦定义了这个中间件，你应该将它添加到路由列表或 app/Http/Kernel.php 文件的全局中间件中。

在你的中间件上调用 terminate 调用时，Laravel 会从服务容器 中解析出一个新的中间件实例。如果要在调用 handle 和 terminate 方法时使用同一个中间件实例，就使用容器的 singleton 方法向容器注册中间件。
