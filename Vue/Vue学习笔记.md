# Vue 个人学习笔记

———— 本文档仅为个人学习笔记

## Vue 介绍

Vue.js（读音 /vjuː/, 类似于 view） 是一套构建用户界面的渐进式框架。

**Vue 只关注视图层**， 采用自底向上增量开发的设计。

Vue 的目标是通过尽可能简单的 API 实现响应的数据绑定和组合的视图组件。

### 框架和库的区别

+ 框架：是一套完整的解决方案；对项目的侵入性较大，项目如果需要更换框架，则需要重新架构整个项目。
+ 库（插件）: 提供某个功能，对项目的侵入性较小，如果某个无法完成某个功能，可以很容易的切换其库。


### MVVM

MVVM 是前端视图层的分层开发思想，主要把每个页面分成了 M、V、VM 三层，VM 是 MVVM中的思想核心，是 M 和 V 之间的调度者。MVVM 思想主要是为了让开发更加方便，因为 MVVM 提供了<u>数据的双向绑定</u>（**由 VM 提供**）。

![MVVM示意图]('https://raw.githubusercontent.com/cayxc/ProgrammingNotes/master/Vue/img/vue-1.jpg')
	
## 安装

**1. 直接用 script 标签  引入**

直接下载并用 script 标签引入，Vue 会被注册为一个全局变量。

	<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

## Vue 实例

每个 Vue 应用都是通过用 Vue 函数创建一个新的 Vue 实例开始的：

	var vm = new Vue({
	  // 选项
	})

### el 挂载点

el 挂载点，Vue 实例要控制页面的区域，即 Vue 开始渲染的容器，Vue 会管理 el 选项命中的元素及其内部的后代元素。如：

	// html结构
	<div id="vm">
    	...
	</div>

	<divclass="app">
    	...
	</div>

	<script>
		var vm = new Vue({
			el:"#vm"
			...	
		})
	
		var app = new Vue({
			el:".app"
			...	
		})
	</script>


**需要注意的 2 点：**

1. 可以使用其他择器（如：class选择器, 标签选择器， id选择器），但建议使用 id 选择器
2. 可以设置其他的**双标签**，**不能挂载到 html 和 body 上**，建议使用 div

### data 数据对象

Vue 中用到的数据定义在 data 中，data 中可以写复杂的数据类型，如：对象，数组等。在渲染复杂类型时，遵循 js 的语法即可。示例：

	// html 结构
	<div id="app">
	    {{ message }}
	    <h2>{{ school.name }} -- {{ school.college }}</h2>
	    <hr>
	    <h2>专业：</h2>
	    <ul>
	        <li>{{ major[0] }}</li>
	        <li>{{ major[1] }}</li>
	        <li>{{ major[2] }}</li>
	    </ul>
	
	</div>

	<script>
		var app = new Vue({
			el: "#app",
			data:{
				message: 'this is a message',
				school:{name:'四川轻化工大学',colllage:'机械工程学院'},
				major:['机械设计','机械制造','机电工程']				
			}
		
		})
	</script>

## Vue 相关指令

### v-cloak 指令

v-cloak 指令结合css样式可以实现以隐藏未编译的插值表达式（解决插值表达式闪烁的问题）标签，直到实例准备完毕。 CSS 规则： [v-cloak] { display: none }

示例：

	[v-cloak] {
	  display: none;
	}

	<div v-cloak>
	  {{ message }}
	</div>


### v-text和 v-html 指令

v-text 指令，用以设置元素的 textContent，与 v-html 指令不同，v-text 会原样输出文本，并不会解析 html 相关标签结构。v-html 指令，用以设置元素的 innerHTML，它会解析相关的 html 标签结构。

**当需要解析 html 结构时使用 v-html，只需输出文本内容时使用 v-text。**

	// html 结构
	<div id="app">
	     <p v-text="text"></p>
   		 <p v-html="content"></p>	
	</div>

	<script>
		var app = new Vue({
	        el:"#app",
	        data:{
	           text: "this is a content..."
	           content: "<a href='http://www.baidu.com' target='_blank'>百度一下</a>"
	        }
    	})
	</script>

### v-on 指令

v-on 的作用是绑定事件，语法：**v-on:js中的事件名="执行的具体方法"**。

v-on 可使用 **@** 简写： v-on:click="执行的具体方法" => @click="执行的具体方法"

methods 和 data 属性是同级的，要执行的方法写在该属性中。

在 methods 中的方法中可以通过 **this.xxx** 获取到 data 属性中 xxx 的值
	
	<div id="app">
	    <button value="v-on绑定事件" v-on:click="doSome">点我试试</button>
	    <button value="v-on绑定事件简写" @click="al">也点我试试</button>
	    <button value="v-on绑定事获取，通过 this 获取 data 数据" @click="getVa">获取 data 数据</button>
	</div>

	<script>
        //1. 创建 vue 实例
    	var app = new Vue({
	        el: "#app",
	        data:{
	            str: "this is custom value!"
	        },
	        methods:{
	            doSome: function () {
	                alert('do something ...');
	            },
	            al:function () {
	                alert('alert some words ...');
	            },
	            getVa: function () {
	                // 可通过 this 获取到 data 中的数据
	                alert(this.str);
	            }
	        }
        })
	</script>

#### v-on 传递自定义参数

使用 v-on 事件绑定的方法写成**函数调用的形式**，可以传入自定义参数。

定义方法时需要定义**形参**来接受传入的实参。

如：

	<div id="app">
    	<p @click="show('中国','你好！')">点我试试</p>
	</div>
	
	<script>
	    // 创建 vue 实例
	   var app = new Vue({
	       el: "#app",
	       methods:{
	           show:function (p1,p2) {
	               alert(p1+p2)
	           }
	       }
	   })
	</script>	

### v-show 指令

v-show 指令的作用：根据表达式的真假切换元素的显示状态。

原理是修改元素的 **display** 的属性。

指令后面的内容会被解析为**布尔值**。

有较高的初始渲染消耗。

为 true 时显示元素，为 false 时隐藏元素。

	<div id="app">
    <p v-show="isShow">被控制元素</p>
    <button @click="hidden">隐 藏</button>
    <button @click="show">显 示</button>
    <p v-show="value>=18">被控制元素2</p>    // v-show中也可是表达式
    <button @click="change">点 击</button>
	</div>
	
	<script>
	    // 创建 vue 实例
	    var app = new Vue({
	        el: "#app",
	        data: {
	            isShow: false,
	            value: 17
	        },
	        methods: {
	            show: function () {
	                this.isShow = true;
	            },
	            hidden: function () {
	                this.isShow = false;
	            },
	            change:function () {
	              this.value++;
	            }
	        }
	    })
	</script>

### v-if 指令

v-if 指令和 v-show 指令的作用一样：根据表达式的真假值显示或隐藏元素，与 v-show 不同之处是：v-if 是直接操作元素，**当表达式为假时会直接删除元素，而 v-show 是操作的元素的 display 属性**，有较高的切换性能消耗。

如果 元素涉及到频繁的切换，最好不要用 v-if。

	<div id="app">
	    <p v-show="isShow">被控制元素1</p>
	    <p v-if="value>=18">被控制元素2</p>
	    <button @click="hidden">隐 藏</button>
	    <button @click="show">显 示</button>
	
	</div>
	
	<script>
	    // 创建 vue 实例
	    var app = new Vue({
	        el: "#app",
	        data:{
	            isShow: true,
	            value: 20
	        },
	        methods:{
	            show:function () {
	                this.isShow = true;
	                this.value = 20;
	            },
	            hidden:function () {
	                this.isShow = false;
	                this.value = 10;
	            }
	        }
	    })
	</script>

### v-bind 指令

v-bind 指令的作用是绑定元素的属性，完整写法是：**v-bind:属性名**，简写方法 **:属性名**。如果是动态的增删 class，建议使用对象的方式。示例： 

	 <style>
        #app > img {
		    display: block;         
		    width: 450px;
			margin: 50px auto;
		    margin-top: 50px;
		    box-shadow: 0 2px 6px 0 #999;
        }
        .active{
            border-radius: 12px;
            box-shadow: 0 2px 6px 0 #999;
        }        
    </style>

	<div id="app">
	    <img v-bind:src="imgSrc" v-bind:title="imgTitle+'--半圆顶'">
	    <img :src="imgSrc" :class="isActive?'active':''" @click="toggleActive">
	    <!-- 三元表达式改写为对象的形式 -->
	    <img :src="imgSrc" :class="{active:isActive}" @click="toggleActive">
	</div>
	
	<script>
	    // 创建 vue 实例
	    var app = new Vue({
	        el: "#app",
	        data:{
	            imgSrc: '../img/01.jpg',
	            imgTitle: 'Half Dome',
	            isActive: false
	        },
	        methods:{
	            toggleActive:function () {
	                this.isActive = !this.isActive;
	            }
	        }
	       
	    })
	</script>

### v-for 指令

v-for 的作用是根据数据生成列表结构，数组，对象经常和 v-for 结合使用。

语法：**（itme,index）in data**

data 可以是 普通数组，对象数组， 对象和数字。

item 和 index 可以结合其他指令一起使用。

数据的更新和同步到页面上，是响应式的。

#### v-for循环数组方式一

**语法:（itme）in data**，示例：

	<div id="app">
	    <ul>
		// 使用方式一:
	        <li v-for="list in province">
	            省份名称：{{ list }}
	        </li>
	    </ul>	   
	</div>
	
	<script>
	    // 创建 vue 实例
	   var app = new Vue({
	       el: "#app",
	       data:{
	           province:['云南','贵州','四川','湖北','湖南','广西','广东']
	       }
	   })
	</script>

#### v-for循环数组方式二

**语法：（itme,index）in data**，示例：

	<div id="app">
	    <ul>		
	        <li v-for="(item,index) in province" :title='index+item'>
	            省份名称{{ index+1 }}：{{ item }}
	        </li>
	    </ul>
	</div>
	
	<script>
	    // 创建 vue 实例
	   var app = new Vue({
	       el: "#app",
	       data:{
	           province:['云南','贵州','四川','湖北','湖南','广西','广东']
	       }
	   })
	</script>

#### v-for循环对象使用方式

**语法：(值,键,索引) in data**，示例：

	<div id="app">	
	    <!-- 循环对象时的使用方式: -->
	    <ul>
	        <li v-for="(val,key,i) in user">
	            值：{{ val }} --- 键：{{ key }} --- 索引：{{ i }}
	        </li>
	    </ul>
	</div>
	
	<script>
	    // 创建 vue 实例
	    var app = new Vue({
	        el: "#app",
	        data: {
	            user:{
	                id:1,
	                name:'lisi',
	                age:'18'
	            }
	        }
	    })
	</script>



#### v-for迭代数字

**语法： v-for="count in data"**

**注意：使用 v-for 迭代数字时， count 是从 1 开始的**。

示例：

	<div id="app">
	    <p v-for="count in 4">这是第 {{ count }} 次循环</p>
	</div>
	
	<script>
	    // 创建 vue 实例
	    var app = new Vue({
	        el: "#app"
	    })
	</script>

	// 执行结果：
	
	这是第 1 次循环
	
	这是第 2 次循环
	
	这是第 3 次循环
	
	这是第 4 次循环


#### v-for 中 key 使用注意事项

> 在 Vue 2.2.0+ 的版本里，**当在组件中使用v-for时，key 属性是必须的。**

当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用 **“就地复用”** 策略。如果数据项的顺序被改变，Vue 将**不是移动 DOM 元素来匹配数据项的顺序**，而是**简单复用此处的每个元素，并且确保他在特定索引下显示已被渲染过的每个元素。**

为了给 Vue 一个提示，**以便它能跟踪每个节点的身份，从而重用和重新排序现有元素**，你需要为每一项提供一个唯一的属性。

	<div id="app">
	    ID: <input type="text" v-model="id">
	    Name: <input type="text" v-model="name">
	    <input type="button" value="添 加" @click="add">
	    <hr>
	    <ul>
	<!-- 注意：-->
	<!-- v-for 循环的时候，key 属性只能使用 number 获取 string ，如下例中的 :key="item.id" -->
	<!-- key 在使用的时候，必须使用 v-bind 属性绑定的形式，指定 key 的值 -->
	<!--在组件中使用 v-for 循环时，或者在一些特殊的情况中，如果 v-for 有问题，必须在使用 v-for 的同时，指定唯一的字符串/数字类型 :key值 -->
	        <li v-for="item in list" :key="item.id">
	            <input type="checkbox">{{ item.id }} ==> {{ item.name }}
	        </li>
	    </ul>
	</div>
	
	<script>
	    var app = new Vue({
	        el:"#app",
	        data:{
	           id: '',
	            name: '',
	            list: [
	                {id: 1,name: '张三'},
	                {id: 2,name: '李四'},
	                {id: 3,name: '王五'},
	                {id: 4,name: '赵六'}
	
	            ]
	        },
	        methods:{
	            add(){
	                this.list.unshift({id:this.id, name:this.name})
	            }
	        }
	    })
	</script>

**使用key时注意事项：**

1. v-for 循环的时候，key 属性只能使用 number 获取 string
2. key 在使用的时候，必须使用 v-bind 属性绑定的形式，指定 key 的值-->
3. 在组件中使用 v-for 循环时，或者在一些特殊的情况中，如果 v-for 有问题，必须在使用 v-for 的同时，指定唯一的字符串/数字类型 :key值。




### v-model 指令（双向数据绑定）

v-model 指令的作用是便捷的设置和获取表单元素的值。绑定的数据和表单元素的值相关联，**数据的绑定是双向的**。

**v-model 只能和表单元素一起使用**。

**只有 v-model 能实现双向数据绑定， v-bind，只能实现单向数据绑定（从 M 绑定到 V）**

	<div id="app">
	    <input type="text" v-model="message">
	    <h2>{{ message }}</h2>  <!-- 此处会和input的value值同步 -->
	</div>
	
	<script>
	    // 创建 vue 实例
	   var app = new Vue({
	       el: "#app",
	       data:{
	           message:'this is a message..'
	       }
	   })
	</script>

## 事件修饰符

v-on 绑定事件时，在事件后面跟上 **.修饰符** 可以对事件进行限制。常用事件修饰符如下：

- **.stop** 阻止冒泡
- **.prevent** 阻止默认事件
- **.capture** 添加事件监听器时使用事件捕获模式
- **.self** 只当事件在该元素本身（比如不是子元素）触发时触发回调，只会阻止自身的冒泡行为，不会阻止其他的冒泡（如：父类的冒泡事件）
- **.once** 事件只触发一次

示例：

	<div id="app">
	    <!-- 使用 .stop 阻止冒泡 -->
	    <!-- <div class="innerDiv" @click="innerClick">
	         <input type="button" value="点我试试" @click.stop="btnClick">
	     </div>-->
	
	    <!-- 使用 .prevent 阻止默认事件-->
	    <!--  <a href="http:\\www.baidu.com" @click.prevent="aClick">百度一下</a>-->
	
	    <!-- 使用 .capture 添加事件监听捕获从外层向里面触发 -->
	    <!-- <div class="innerDiv" @click.capture="innerClick">
	        <input type="button" value="点我试试" @click="btnClick">
	    </div>-->
	
	    <!-- 使用 .self 在该元素本身触发,只会阻止自身的冒泡行为，不会阻止其他的冒泡行为（如：父类的冒泡事件） -->
	    <!-- <div class="innerDiv" @click.self="innerClick">
	         <input type="button" value="点我试试" @click="btnClick">
	     </div>-->
	
	    <!-- 使用 .once 事件只触发一次 -->
	    <a href="http:\\www.baidu.com" @click.prevent.once="aClick">百度一下</a>
	</div>
	
	
	<script>
	    var app = new Vue({
	        el: "#app",
	        methods: {
	            btnClick() {
	                console.log('点击了btn');
	            },
	            innerClick() {
	                console.log('点击了innerDiv');
	            },
	            aClick() {
	                console.log('点击了链接');
	            }
	        }
	    })
	</script>

下例中的 **.enter** 的作用是限制触发的按键为 enter

如：

	<div id="app">
	    <input type="text" @keyup.enter="xs('老铁：')">
	</div>
	
	<script>
	    // 创建 vue 实例
	   var app = new Vue({
	       el: "#app",
	       methods:{         
	           xs:function (p1) {
	               alert(p1+'确定提交吗?');
	           }
	       }
	   })
	</script> 

## Vue 中使用样式

### 使用 class 样式

#### 数组方式

	<h1 :class="['red','thin']">使用数组方式</h1>

####  数组中使用三元表达式

	<h1 :class="['red','thin',isActive?'active':'']">数组中使用三元表达式</h1>

#### 数组中嵌套对象

	<h1 :class="['red','thin',{'active':isActive}]">数组中嵌套对象</h1>

#### 直接使用对象

	<h1 :class="{'red':true,'thin':true,active:true}">直接使用对象</h1>

### 使用内联样式

#### 方式一

直接在元素上通过 :style 形式，书写对象样式

	<h1 :style="{color:'red','font-size':'60px'}">通过 :style 使用内联样式</h1>

#### 方式二

将样式对象化，定义到 data 中，直接应用到 :style 中

	<div id="app">
	    <h1 :style="styleObj">将样式对象化，直接引用到 :style 中</h1>
	</div>
	
	<script>
	    var app = new Vue({
	        el:"#app",
	        data:{
	            styleObj: {color:'blue','font-size':'40px'}
	        }
	    })
	</script>

#### 方式三

在 :style 中通过数组，引用多个 data 上的样式对象

	<div id="app">
	    <h1 :style="[styleObj,styleObj2]">:style 中通过数组引用多个将样式对象</h1>
	</div>
	
	<script>
	    var app = new Vue({
	        el:"#app",
	        data:{
	            styleObj: {color:'blue','font-size':'40px'},
	            styleObj2: {background:'green','font-weight':'normal'},
	        }
	    })
	</script>

## 过滤器

Vue.js 允许自定义过滤器，**可用作一些常见的文本格式化**。过滤器可以用在两个地方：**Mustache插值表达式和 v-bind 表达式**。过滤器应该被添加在 JavaScript 调用的实例之前，由“管道”符指示。

过滤器有两种: **全局过滤器、私有过滤**。

### 全局过滤器

定义语法： ```Vue.filter('过滤器名称', function(){})```

过滤器中的 function 中：第一个参数，已经规定死了，永远都是过滤器管道符前面传递过来的数据。

示例：

	<!-- HTML结构 -->
	<div id="app">
	    <!-- 调用过滤器 -->
	    {{ message | msgHandle }}
	</div>
	
	<script>
	    // 定义全局过滤器
	    // 注意：要在 app 之前定义
	    Vue.filter('msgHandle', function (data) {
	        return data.toUpperCase();
	    });
	
	    var app = new Vue({
	        el: "#app",
	        data: {
	            message: 'this is a message'
	        }
	    });
	</script>

### 私有（局部）过滤器

定义语法： 在实例中添加filters属性：filters:{过滤器名称1：处理函数，过滤器名称2：处理函数，...}。

私有过滤器只在所定义的实例中有效。

示例：

	<!-- HTML结构 -->
	<div id="app">
	    <!-- 调用过滤器 -->
	    {{ message | msgHandle }}
	</div>
	
	<script>
	    var app = new Vue({
	        el: "#app",
	        data: {
	            message: 'this is a message'
	        },
		filters:{
	            msgHandle:function (data,parrern='...') {
	                return data+parrern;
	            }
	        }
	    });
	</script>

**注意：**过滤器调用采用的是**就近原则**，如果私有过滤器和全局过滤器同名，则优先调用私有过滤器。

## 按键修饰符

在使用 v-on 进行事件绑定时，可以指定按键修饰符，如在点击 enter 键时触发事件，enter 也可使用 按键码代替：enter => 13，[全部键盘码参考]('https://www.cnblogs.com/yiven/p/7118056.html')

	<input type="button" value="点我试试" @click.enter="doSome">
	// 与上同效果
	<input type="button" value="点我试试" @click.13="doSome">	

### 自定义全局键盘修饰符

Vue.js 支持自定义键盘修饰符，如定义键盘码值为 113 的 f2 键 ：

	Vue.config.keyCodes.f2 = 113;
	// 使用自定义键盘修饰符
	<input type="button" value="点我试试" @keyup.f2="doSome">	

## 自定义指令

除了核心功能默认内置的指令 (v-model 和 v-show)，Vue 也允许注册自定义指令。

### 自定义全局指令

定义全局指令，语法： **Vue.directive(param1,param2)**

param1： 指令的名称，**注意：在定义指令名称时，不需要加 v- 前缀，但在调用时必须加上 v- 前缀。**

param2： 是一个对象，这个对象上，有一些指令相关的函数，这些函数可以在特定的阶段，执行相关的操作。

示例：

	<div id="app">
	    <input type="text" v-focus>
	</div>
	
	<script>
	    // 注册全局指令
	    Vue.directive('focus', {
	        inserted:function (el) {
	            // 聚焦元素
	            el.focus();
	        }
	    });
	    var vm = new Vue({
	        el: "#app",
	
	    })
	</script>

### 自定义局部指令

如果想注册局部指令，组件中也接受一个 directives 的选项，示例：

	<div id="app">
	    <input type="text" v-bdcolor>
	</div>
	
	<script>
	    var vm = new Vue({
	        el: "#app",
	        // 注册局部指令
	        directives:{
	            bdcolor:{
	                bind: function (el) {
	                    el.style.border='2px solid red';
	                }
	            }
	        }
	
	    })
	</script>

### 钩子函数

一个指令定义对象可以提供如下几个钩子函数 (均为可选)：

- **bind：**只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。

- **inserted：**被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。

- **update：**所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。

- **omponentUpdated：**指令所在组件的 VNode 及其子 VNode 全部更新后调用。

- **unbind：**只调用一次，指令与元素解绑时调用。

接下来我们来看一下钩子函数的参数 (即 el、binding、vnode 和 oldVnode)。

### 钩子函数参数

指令钩子函数会被传入以下参数：

- **el：**指令所绑定的元素，可以用来直接操作 DOM 。
- **binding：**一个对象，包含以下属性：
	- <u>**name：**指令名，不包括 v- 前缀。</u>
	- <u>**value：**指令的绑定值，例如：v-my-directive="1 + 1" 中，绑定值为 2，**（计算后的值）**</u>
	- **oldValue：**指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
	- <u>**expression：**字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"，**（原样输出）**</u>
	- **arg：**传给指令的参数，可选。例如 v-my-directive:foo 中，参数为 "foo"。
	- **modifiers：**一个包含修饰符的对象。例如：v-my-directive.foo.bar 中，修饰符对象为 { foo: true, bar: true }。
- **vnode：**Vue 编译生成的虚拟节点。
- **oldVnode：**上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。

> 除了 el 之外，其它参数都应该是只读的，切勿进行修改。如果需要在钩子之间共享数据，建议通过元素的 dataset 来进行。

这是一个使用了这些属性的自定义钩子样例：

	<div id="hook-arguments-example" v-demo:foo.a.b="message"></div>
	Vue.directive('demo', {
	  bind: function (el, binding, vnode) {
	    var s = JSON.stringify
	    el.innerHTML =
	      'name: '       + s(binding.name) + '<br>' +
	      'value: '      + s(binding.value) + '<br>' +
	      'expression: ' + s(binding.expression) + '<br>' +
	      'argument: '   + s(binding.arg) + '<br>' +
	      'modifiers: '  + s(binding.modifiers) + '<br>' +
	      'vnode keys: ' + Object.keys(vnode).join(', ')
	  }
	})
	
	new Vue({
	  el: '#hook-arguments-example',
	  data: {
	    message: 'hello!'
	  }
	})

	// 执行结果：
	name: "demo"
	value: "hello!"
	expression: "message"
	argument: "foo"
	modifiers: {"a":true,"b":true}
	vnode keys: tag, data, children, text, elm, ns, context, fnContext, fnOptions, fnScopeId, key, componentOptions, componentInstance, parent, raw, isStatic, isRootInsert, isComment, isCloned, isOnce, asyncFactory, asyncMeta, isAsyncPlaceholder

### 函数简写

在很多时候，你可能想在 bind 和 update 时触发相同行为，而不关心其它的钩子。可以这样写:

	Vue.directive('color-swatch', function (el, binding) {
	  el.style.backgroundColor = binding.value
	})

### 对象字面量

如果指令需要多个值，可以传入一个 JavaScript 对象字面量。记住，指令函数能够接受所有合法的 JavaScript 表达式。

	<div v-demo="{ color: 'white', text: 'hello!' }"></div>
	Vue.directive('demo', function (el, binding) {
	  console.log(binding.value.color) // => "white"
	  console.log(binding.value.text)  // => "hello!"
	})

## Vue生命周期

Vue 生命周期：Vue 实例从创建、运行到销毁期间，总是伴随着各种各样的事件，这些事件统称为生命周期。

- **生命周期钩子**：就是生命周期事件的别名。
- 生命周期钩子 = 生命周期函数 = 生命周期事件
- 主要的生命周期函数分为 3 大类：
	- **1. 创建期间的生命周期函数：**
		- **beforeCreate：**实例刚在内存中创建出来，此时，还没没有初始化好 data 和 methods 属性
		- **created：** 实例已经在内存中创建OK,此时，data 和 methods 已经创建OK，还未开始编译模板 
		- **beforeMount：**此时模板已编译完成，但还未挂载到页面中
		- **mounted：** 此时已将编译好的模板挂载到了页面指定的容器中显示
	- **2. 运行期间的生命周期函数：**
		- **beforeUpdate：**状态更新之前运行此函数，**此时 data 中的状态值是最新的，但是界面上显示的还是旧的数据**，因为此时还未开始渲染新的DOM节点
		- **updated：** 实例更新完成之后调用此函数，此时 data 中的数据和页面上显示的数据都已完成了更新，界面也已经被重新渲染好了
	- **3. 销毁期间的生命周期函数 ：**
		- **beforeDestory：**实例销毁之前调用。在这一步，实例仍然可以调用
		- **destoryed：** Vue实例被销毁后调用。调用后，Vue 实例的所有东西都会被移除和解除绑定（如：事件监听器，指令等），所有的子实例也会被销毁。

![Vue生命周期]('https://raw.githubusercontent.com/cayxc/ProgrammingNotes/master/Vue/img/lifecycle.png')