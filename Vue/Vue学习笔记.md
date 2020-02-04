# Vue 个人学习笔记

———— 本文档仅为个人学习笔记

## Vue 介绍

Vue.js（读音 /vjuː/, 类似于 view） 是一套构建用户界面的渐进式框架。

Vue 只关注视图层， 采用自底向上增量开发的设计。

Vue 的目标是通过尽可能简单的 API 实现响应的数据绑定和组合的视图组件。

	
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

el 挂载点，即 Vue 开始渲染的容器，Vue 会管理 el 选项命中的元素及其内部的后代元素。如：

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
2. 可以设置其他的<u>双标签</u>，**不能挂载到 html 和 body 上**，建议使用 div

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


### v-text和 v-html 指令

v-text 指令，用以设置元素的 textContent，与 v-html 指令不同，v-text 会原样输出文本，并不会解析 html 相关标签结构。v-text 指令，用以设置元素的 innerHTML，它会解析相关的 html 标签结构。

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

v-on 的作用是绑定事件，语法：v-on:js中的事件名="执行的具体方法"。

v-on 可使用 @ 简写： v-on:click => @click

methods 和 data 属性是同级的，要执行的方法写在该属性中。

在 methods 中的方法中可以通过 **this.xxx** 获取到 data 属性中的值
	
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

**v-on 传递自定义参数**

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

**v-on 事件修饰符**

v-on 绑定事件时，在事件后面跟上 **.修饰符** 可以对事件进行限制。

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
	

### v-show 指令

v-show 指令的作用：根据表达式的真假切换元素的显示状态。

原理是修改元素的 **display** 的属性。

指令后面的内容会被解析为**布尔值**。

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

v-if 指令和 v-show 指令的作用一样：根据表达式的真假值显示或隐藏元素，与 v-show 不同之处是：v-if 是直接操作元素，当表达式为假时会直接删除元素，而 v-show 是操作的元素的 display 属性。

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

v-bind 指令的作用是绑定元素的属性，完整写法是：**v-bind:属性名**，简写方法 **:属性名**。如果是动态的增删 class，建议使用对象的方式。示例如： 

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

v-for 的作用是根据数据生成列表结构，数组经常和 v-for 结合使用。

语法：**（itme,index）in 数据**

item 和 index 可以结合其他指令一起使用。

数据的更新和同步到页面上，是响应式的。

示例：

	<div id="app">
	    <ul>
	        <li v-for="item in province">
	            省份名称：{{ item }}
	        </li>
	    </ul>
	    <hr>
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


### v-model 指令

v-model 指令的作用是便捷的设置和获取表单元素的值，**需要和表单元素一起使用**。

绑定的数据和表单元素的值相关联，**数据的绑定是双向的**

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
