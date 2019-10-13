<font size=6>**Laravle5.6学习笔记**</font>

# 安装 #

## 通过composer安装 ##

	composer create-project --prefer-dist laravel/laravel blog

## 安装laravel-debugbar调试工具 ##

1. 在packagist中搜索laravel-debugbar
2. 项目中执行composer require barryvdh/laravel-debugbar
3. 在config/app.php加入： `Barryvdh\Debugbar\ServiceProvider::class，`
4. 线上时注释掉第三步的代码


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

## 排除指定路由中不进行CSRF验证 ##

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
	
5.在服务器更新资源 ---> Route::put/patch($rui, $callback);

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

Laravel控制器采用<font color=red>大驼峰命名规范</font>，同时注意命名空间和基类控制器的引入

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

## Input类来获取数据 ##

通过接受用户输入的类：<u>Illuminate\Support\Facades\Input</u>来进行数据获取：

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


当服务器收到浏览器的请求后，会发送响应消息给浏览器。

## 返回字符串 ##

在控制器或路由中 echo 或 return 一个字符串就可以了。 

## 设置cookie ##

Laravel框架为了安全，它的cookie是加密的。注意response()中必须有内容，没有也要填一个空字符串，如：response('')

	// 设置 cookie 
	return response('')->cookie('id',111,10.'/');
	// 读取 cookie
	echo request->cookie('id');

## 重定向 ##

根据路由的别名，跳转到指定的位置：

	retrn redirect()->route('user.center');
	//另一种写法
	return redirect(route(''user.center'));

	//带参数
	return redirect()->route('user.cneter',['id'=>1]);
	return redirect(route(''user.center',['id'=>1]));

## Json数据返回 ##
	
	//参数1 数组
	//状态码，默认200
	return response()->json(['id'=>1,'name'=>'张三']，201);

# 视图 #

视图的作用就是用来存放应用程序中 HTML 内容，并且能够将你的控制器层与展示层分开。我们在控制器中使用助手函数 view() 来加载视图模板。

## 视图目录位置 ##

视图文件放置在<u>项目目录、resources/views</u> 文件夹中

## 视图命名 ##

视图文件是以 .blade.php为后缀名

	// 一定要写模板的名称  模板在 /resources/views/index.blade.php
    return view('index');

如果视图文件是在 resources/views</u> 下的不同模块中，如在 resources/views/html/</u> 文件夹中，可按照如下写法。提示：可以多层目录。

	return view('html.index'); // 推荐写法
    return view('html/index'); // 不推荐

## 分配数据到模板 ##

### 关联数组的形式来传值 ###
	
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

### compact【推荐】 ###

	//控制器中
	$data = ['id'=>1,'name'=>'张三'];
    return view('html.index',compact('data'));
	//模板中
	<p>ID：{{ $data['id'] }}</p>
    <p>Name：{{ $data['name'] }}</p>

### with ###

	//控制器中
	$data = ['id'=>1,'name'=>'张三'];
    return view('html.index')->with(['data'=>$data]);
	//模板中
	<p>ID：{{ $data['id'] }}</p>
    <p>Name：{{ $data['name'] }}</p>

## 模板中输出变量 ##

{{$变量名}}，如：{{$name}}

	<p>Name：{{ $data['name'] }}</p>

### 三元运算 ###

{{ $name or 'default' }} 等价于 <?php> echo isset($name)?$name:'default' ?>

	<p>年龄：{{ $data['age'] or '没有定义' }}</p>
	{{-- 建议在laravel5.7之后用 --}}  // Blade模板引擎注释风格
    <p>年龄：{{ $data['age'] ?? '没有定义' }}</p>

### 未转义输出 ###

如果变量信息里有html标记信息，在输出的时候html标记会被转化为实体符号，而没有被浏览器解析掉，如果希望看到被浏览器解析后的内容，就需要设置两个<font color=red>俩个感叹号</font>，如：{!! $变量名 !!}

	//控制器中
	$data = ['id'=>1,'name'=>'张三'];
    $tite = "<a href='www.baidu.com'>百度一下</a>";
    return view('html.index',compact('data','title'));
	//模板中
	<p>{!! $title !!}</p>

### 原始形态输出 ###

由于很多 Javascript 框架都使用花括号来表明所提供的表达式，所以你可以使用 @ 符号来告知Blade渲染引擎你需要保留这个表达式的原始形态，如：@{{ $变量名 }}

## 使用函数 ##

可以在 Blade 模板中直接使用 php 函数，{{php函数名}}

	<p>{{ date('Y-m-d') }}</p>

## if语句 ##

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

## Switch语句 ##

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

## 循环 ##

### for循环 ###

	@for ($i = 0; $i < 10; $i++)
	    目前的值为 {{ $i }}
	@endfor

### foreach循环 ###

	@foreach ($users as $user)
	    <p>此用户为 {{ $user->id }}</p>
	@endforeach

### forelse循环 ###

	@forelse ($users as $user)
	    <li>{{ $user->name }}</li>
	@empty
	    <p>没有用户</p>
	@endforelse

### while循环 ###

	@while (true)
	    <p>死循环了</p>
	@endwhile

### 循环其他事项 ###

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

### 循环变量 ###

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

## 模板包含  ##

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

## 模板继承  ##

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

# 表单验证 #

表单验证是为了防止访问者跳过客户端验证而造成系统安全问题，一旦非法用户绕过客户端验证而服务器端没有验证，就很不安全，所以项目必须要进行服务器端表单验证。

Laravel提供了多种不同的验证方法来对应用程序传入的数据进行验证。

## 验证规则 ##

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
unique | 表面，字段，需要排除的ID
between:min,max | 验证字段值的大小是否介于指定的min和max之间。字符串、数值或是文件大小的计算方式和size规则相同

## 表单的CSRF验证 ##

表单验证前，我们要对表单的CSRF验证进行处理：

	//只生成token值没有html代码，适用于 ajax提交
	{{ csrf_token }} 

	//生成token的同时还生成了html代码 适用于一般的表单提交
	{{ csrf_field() }} 或 {{ @csrf }} ( >=5.6版本写法 )  

![表单TOKEN](https://raw.githubusercontent.com/CayangPro/my_notes/master/Laravel/img/yz-1.jpg)

## $this->validate() 验证 ##

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

## 独立方式验证 ##

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

## 验证器方式验证【推荐】 ##

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

## 模板中输出错误信息 ##

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

## 错误提示换成中文 ##

1. 在packagist网站：https://packagist.org搜索lang
2. 选择：caouecs/laravel-lang(Languages for Laravel)
3. 进入项目的gitHub地址
4. 下载src目录中的zh-CN目录
5. 将zh-CN目录放在Laravel的resources\lang目录下
6. 将config\app.php中的 'locale' => 'en' 改为 'locale' => 'zh-CN' 

# 数据库 #

## Laravel支持的数据库类型 ##

Laravel 能使用原生 SQL、查询构造器 和 Eloquent ORM 在各种数据库后台与数据库进行非常简单的交互。当前 Laravel 支持四种数据库:

- MySQL
- Postgres
- SQLite
- SQL Server

Laravel操作数据库所使用的扩展是PDO扩展，一定要开启PDO扩展，PHP配置文件中打开PDO扩展：extension=php_pdo_mysql.dll

## 配置连接数据库信息 ##

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
- 在 .env 文件中来接数据库的位置加上：DB_PREFIX=表前缀

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

## 检查数据库配置是否正确 ##

	ROute::get('pdo',functon(){
		dump(\DB::connection());
	});

## DB类执行原生SQL语句 ##

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

## 查询构造器操作数据库 ##

Laravel 的查询构造器使用 PDO 参数绑定来保护您的应用程序免受 SQL 注入攻击。因此没有必要清理作为绑定传递的字符串。

<font color=red>注：使用查询构造器时表名不需要写表前缀。</font>

### 查询 ###

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

### 添加 ###

查询构造器还提供了 insert 方法用于插入记录到数据库中。 insert 方法接收数组形式的字段名和字段值进行插入操作，返回true或false：

	DB::table('users')->insert(
		['email' => 'john@example.com', 'votes' => 0]
	);

还可以在 insert 中传入一个嵌套数组向表中插入多条记录。每个数组代表要插入表中的行：

	DB::table('users')->insert([
	    ['email' => 'taylor@example.com', 'votes' => 0],
	    ['email' => 'dayle@example.com', 'votes' => 0]
	]);
		
#### 自增 ID ####

如果数据表有自增 ID，使用 insertGetId 方法来插入记录并返回 ID 值：

	$id = DB::table('users')->insertGetId(
	    ['email' => 'john@example.com', 'votes' => 0]
	);

### 更新 ###

查询构造器也可通过 update 方法更新已有的记录，返回受影响的行数。 update 方法和 insert 方法一样，接受包含要更新的字段及值的数组。 你可以通过 where 子句对 update 查询进行约束：

	DB::table('users')
            ->where('id', 1)
            ->update(['votes' => 1]);

#### 更新 JSON 字段 ####

更新 JSON 字段时，你可以使用 -> 语法访问 JSON 对象上相应的值，该操作只能用于支持 JSON 字段类型的数据库：

	DB::table('users')
            ->where('id', 1)
            ->update(['options->enabled' => true]);

#### 自增与自减 ####

查询构造器还为给定字段的递增或递减提供了方便的方法。 此方法提供了一个比手动编写 update 语句更具表达力且更精练的接口。

这两个方法都至少接收一个参数：需要修改的列。第二个参数是可选的，用于控制列递增或递减的量。

	DB::table('users')->increment('votes');
	
	DB::table('users')->increment('votes', 5);
	
	DB::table('users')->decrement('votes');
	
	DB::table('users')->decrement('votes', 5);

也可以在操作过程中指定要更新的字段：

	DB::table('users')->increment('votes', 1, ['name' => 'John']);

### 删除 ###

查询构造器也可以使用 delete 方法从数据表中删除记录。在使用 delete 前，可添加 where 子句来约束 delete 语法：

	DB::table('users')->delete();
	
	DB::table('users')->where('votes', '>', 100)->delete();
	
	//简写，主键名必须是id
	DB::table('users')->delete(10);

如果你需要清空表，你可以使用 truncate 方法，这将删除所有行，并重置自增 ID 为零：

	DB::table('users')->truncate();

#### 悲观锁 ####

查询构造器也包含一些可以帮助你在 select 语法上实现 「悲观锁定」的函数。若想在查询中实现一个「共享锁」，你可以使用 sharedLock 方法。共享锁可防止选中的数据列被篡改，直到事务被提交为止 ：

	DB::table('users')->where('votes', '>', 100)->sharedLock()->get();	

另外，你也可以使用 lockForUpdate 方法。使用「更新」锁可避免行被其它共享锁修改或选取：

	DB::table('users')->where('votes', '>', 100)->lockForUpdate()->get();

## 数据库迁移 ##

迁移就像是数据库的版本控制，允许团队简单轻松的编辑并共享应用的数据库表结构，迁移通常和 Laravel 的 数据库结构生成器配合使用，让你轻松地构建数据库结构。如果你曾经试过让同事手动在数据库结构中添加字段，那么数据库迁移可以让你不再需要做这样的事情。

就是使用php文件编写的代码来进行数据库表结构的创建和修改，可以做到快速部署、永久保存、方便管理数据库。


### 生成与编写迁移 ###

使用 Artisan 命令 make:migration 来创建迁移。

新的迁移位于 <u>database/migrations</u> 目录下。每个迁移文件名都包含时间戳，以便让 Laravel 确认迁移的顺序。

--table 和 --create 选项可用来指定数据表的名称，或是该迁移被执行时是否将创建的新数据表。这些选项需在预生成迁移文件时填入指定的数据表：
	
	php artisn make:migration create_users_table
	
	create_users_table   生成的文件后缀名称

	php artisan make:migration create_users_table --create=test
	
	--create=test   生成表名为 test 的数据表

如果你想要指定生成迁移指定一个自定义输出路径，则可以在运行 make:migration 命令时添加 --path 选项，给定的路径必须是相对于应用程序的基本路径。


### 迁移结构 ###

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

### 执行数据迁移 ###

使用 Artisan 命令 migrate 方法来运行所有未完成的迁移：

	php artisan migrate

#### 在生产环境强制执行迁移 ####

一些迁移操作是具有破坏性的， 这意味着可能会导致数据丢失。 为了防止有人在生产环境中运行这些命令， 系统会在这些命令被运行之前与你进行确认。如果要强制忽略系统的提示运行命令， 则可以使用 --force 标记：

	php artisan migrate --force

### 回滚迁移 ###

若要回滚最后一次迁移， 可以使用 rollback 命令。 此命令将回滚最后一次 “迁移” 的操作，其中可能包含多个迁移文件：

	php artisan migrate:rollback

可以在 rollback 命令后面加上 step 参数，来限制回滚迁移的个数。 例如，以下命令将回滚最近五次迁移：

	php artisan migrate:rollback --step=5

migrate:reset 命令可以回滚应用程序中的所有迁移：
	
	php artisan migrate:reset

#### 使用单个命令来执行回滚或迁移 ####

migrate:refresh 命令不仅会回滚数据库的所有迁移还会接着运行 migrate 命令。 这个命令可以高效地重建整个数据库：

	php artisan migrate:refresh 

	// 刷新数据库结构并执行数据填充
	php artisan migrate:refresh --seed

使用 refresh 命令并提供 step 参数来回滚并再执行最后指定的迁移数。例如， 以下命令将回滚并重新执行最后五次迁移：

	php artisan migrate:refresh --step=5

#### 删除所有表 & 迁移 ####

migrate:fresh 命令会从数据库中删除所有表，然后执行 migrate 命令:

	php artisan migrate:fresh

	php artisan migrate:fresh --seed

### 数据表 ###

#### 可用的字段类型 ####

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

#### 字段修饰 ####

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
