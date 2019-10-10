<font size=6>**Laravle5.6学习笔记**</font>

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

<font color=red>注：多个验证规则可以通过 | 字符进行隔开</font>，以下为常用验证规则

规则 | 说明
:- | :-
required | 不能为空
max:value | 字段值必须 <= value，对于字符串来说，value为字符数
min:value | 字段值必须 <= value，对于字符串来说，value为字符数
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

