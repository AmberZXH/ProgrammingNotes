<font size=6>**JavaScript学习笔记**</font>

——————
版权声明： 本笔记仅为个人学习笔记，文中部分来源于CSDN博主「陈大鱼头」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。

[原文链接：https://blog.csdn.net/weixin_37615279/article/details/103607208](https://blog.csdn.net/weixin_37615279/article/details/103607208)

## 介绍

ECMAScript是一种由ECMA国际（**European Computer Manufacturers Association 欧洲计算机制造商协会**）在标准ECMA-262中定义的脚本语言规范。这种语言在万维网上应用广泛，它往往被称为JavaScript或JScript，但实际上后两者是ECMA-262标准的实现和扩展。JavaScript实现由下列三个不同部分组成：

1. 核心 （ECMAScript）
2. 文档对象模型 （DOM）
3. 浏览器对象模型 （BOM）

### 历史版本

目前有九个ECMA-262版本发表，如下：

- 1997年6月：第一版
- 1998年6月：修改格式，使其与ISO/IEC16262国际标准一样
- 1999年12月：强大的正则表达式，更好的词法作用域链处理，新的控制指令，异常处理，错误定义更加明确，数据输出的格式化及其它改变
- 2009年12月：添加严格模式("use strict")。修改了前面版本模糊不清的概念。增加了getters，setters，JSON以及在对象属性上更完整的反射。
- 2011年6月：ECMAScript标5.1版形式上完全一致于国际标准ISO/IEC 16262:2011。
- 2015年6月：ECMAScript 2015（ES2015），第 6 版，最早被称作是 ECMAScript 6（ES6），添加了类和模块的语法，其他特性包括迭代器，Python风格的生成器和生成器表达式，箭头函数，二进制数据，静态类型数组，集合（maps，sets 和 weak maps），promise，reflection 和 proxies。作为最早的 ECMAScript Harmony 版本，也被叫做ES6 Harmony。
- 2016年6月：ECMAScript 2016（ES2016），第 7 版，多个新的概念和语言特性。
- 2017年6月：ECMAScript 2017（ES2017），第 8 版，多个新的概念和语言特性。
- 2018年6月：ECMAScript 2018 （ES2018），第 9 版，包含了异步循环，生成器，新的正则表达式特性和 rest/spread 语法。
- 2019年6月：ECMAScript 2019 （ES2019），第 10 版。

### 发展标准

**TC39（Technical Committee 39），39号技术委员会**，是一个推动JavaScript发展的委员会，它的成语来自各个主流浏览器的代表成语。会议实行多数决，每一项决策只有大部分人同意且没有强烈反对才能去实现。

每一项新特性最终要进入到ECMAScript规范里，需要经历5个阶段，这5个阶段如下：

- Stage 0: Strawperson， 只要是TC39成员或者贡献者，都可以提交想法

- Stage 1: Proposal， 这个阶段确定一个正式的提案

- Stage 2: draft， 规范的第一个版本，进入此阶段的提案大概率会成为标准

- Stage 3: Candidate， 进一步完善提案细则

- Stage 4: Finished， 表示已准备好将其添加到正式的ECMAScript标准中

由于ES6以前的属性诞生年底久远，我们使用也比较普遍，遂不进行说明，ES6之后的语言风格跟ES5以前的差异比较大，所以单独拎出来做个记录。

> ES6(ES2015) ES6是一次重大的革新，比起过去的版本，改动比较大，本文仅对常用的API以及语法糖进行讲解。

## 严格模式

ECMAScript 5 引入了严格模式（strict mode）的概念。

要开启严格模式，可以在顶部加入如下代码：

	"use strict";

也可定义函数在严格模式下执行：

	function myfun(){
		"use strict";
		...	
	}

## 关键字和保留字

ECMAScript-262 描述了一组具有特定用途的**关键字**，用于表示控制语句的开始或结束，或执行特定的操作等。

ECMAScript-262 还描述了一组不能用作标志符的**保留字**，尽管还没有任何特定用途，但将来可能被使用。

**使用关键字时会出现"Identifier Expected" 错误，使用保留字可能会出现相同错误，取决于特定的引擎。**

## 数据类型

ECMAScript 中有**5种简单的数据类型（也称为基本数据）**和**1种复杂数据类型**。

5种简单的数据类型：

- Unidefined
- Null
- Boolean
- Number
- String

1种复杂数据类型：

- Objdect，Object本质上是一组由无序的名值对组成的。

### typeof 操作符

由于ECMAScript是松散类型的，所谓松散类型就是可以用来保存任何值（未保存初始值的变量会保存一个特殊值——undefined），检测变量的数据类型使用 typeof。

对一个值使用 typeof可能返回下列某个字符串：

- "undefined" —— 如果这个值未定义
- "boolean" —— 如果这个值是布尔值
- "string" —— 如果这个值是字符串
- "unmber" —— 如果这个值是数值
- "object" —— 如果这个值是对象或**null**
- "function" —— 如果这个值是函数

### Undefined 类型

Undefined 类型只有一个值，即特殊的undefined。

对于尚未声明过的变量，只能执行一项操作，即使用 typeof 操作检测数据类型。

对未申明的变量调用 delete 不会导致错误，在严格模式下确实会导致错误。


### Null 类型

Null 类型是第二个只有一个值的数据类型，该值为 null，null值表示一个空对象指针，所以使用 typeof 检测时会返回 “Object”。

	var car = null;
	alert(typeof car);  // "object"

**如果定义的变量准备在将来用于保存对象，那么最好将该变量的初始值设为 null 而不是其他值。**

### Boolean 类型

Boolean只有两个字面量值： true 和 false，这两个值与数字值不是一回事，ture 不一定等于 1，而 false 也不一定等于 0，true 和 fasle 是严格区分大小写的（Ture 和 False 等其他大小写混合形式都不是Boolean的值）；

将值转换为对应的 Boolean 值，可使用 Boolean() 函数，如下：

	var mes = "hello world";
	var mesAsBoolean = Boolean(mes); 

**各数据类型及其对应的转换规则：**

数据类型 | 转换为 true 的值 | 转换为 false 的值
:- | :- | :-
Boolean | true | fasle
Sring | 任何非空字符串 | ""（ 空字符串，注意 " " 会转为 true ）
Number | 任何非 0 数字值（包括无穷大） | 0 和 NaN
Object | 任何对象 | null
Undefined | n/a(not applicable，不适用) | undefined

### Number 类型

#### 浮点数

所谓浮点数值，就是该数值中必须包含一个小数点，并且小数点后面至少有一位数字。

	var flaotNum1 = 1.1;
	var floatNum2 = 0.1;
	var floatNum3 = .1;  // 有效，但不推荐

<font color=red>注意：</font>

如果小数点后面没有跟任何数字，那么这个数值可以作为整数值保存。

如果浮点数本身表示的就是一个整数（1.0），那么，也会被转为整数。

	 var floatNum1 = 1.;   // 小数点后没有数字，解析为 1
	 var floatNum2 = 10.0  // 解析为整数

对于极小或极大的数值，可用 e 表示法（科学计数法）表示。用 e 表示法表示的数值等于 e 前面的数值乘以 10 的指数自幂。

	var num1 = 3.125e7;  // 等于 31250000
	var num2 = 3e-5;     // 等于 0.00003


#### 数值范围

说明 | 参数 | 取值
:-|:-|:-
最小值 | Number.MIN_VALUE | 5e-324
最大值 | Number.MAX_VALUE | 1.7976931348623157e+508

如果某次计算的结果超出了数值范围，将自动转换为特殊的 **Infinity（正无穷）/ -Infinity（负无穷）**，判断一个值是否是有穷的，使用 **isFinite():**

	var result = Number.MAX_VALUE;
	alert(isFinite(result));  // false

#### NaN

NaN（Not a Number）非数值的一个特殊的数值，判断一个参数是否“不是数值”使用 **isNaN()** ，有两个特点：

- 任何涉及NaN的操作都会返回NaN
- NaN与任何值都不相等，**包括NaN自身**

#### 数值转换

把非数字转换为数值： Number()、 parsetInt()、parsetFloat()

转型函数Number()，可用于任何类型；另外两个专门用于把字符串转为数值。

**Number() 转换规则：**

原始值 | 转换结果说明 
:-|:-
Boolean | true和false转为：1/0
Number | 返回原始值
null | 0
undefined | NaN
String（只含数字，包括正负号情况） | 十进制数值，忽略前导零，'123' => 123，'011' => 11
String（包含有效浮点格式） | 对应的浮点数值，忽略前导零
String（包含十六进制格式，包括正负号情况） | 相同大小的十进制整数，如：'0xf' => 15
String（空字符串，不含任何字符） | 0
上述之外的字符 | NaN
Object | 调用 valueof() 方法，按照前面的规则转换，如果结果为NaN，调用对象的 toString() 方法，按前面的规则返回

### String 类型

在js中，单、双引号的解析效率是一样的。

#### 字符串字面量

**特殊字面量：**

字面量 | 含义
:- | :- 
\n | 换行
\t | 制表
\b | 退格
\r | 回车
\f | 进纸
\\ | 斜杠
\' | 单引号（'）
\" | 双引号（"）
\xnn | 以十六进制代码nn表示的一个字符（其中n为0~F），如： \x41表示 "A"
\unnnn | 以十六进制代码nnnn表示的一个Unicode字符（其中n为0~F），如： \u03a3表示 Σ

#### 转换为字符串

把一个值转换为字符串有两种方式：

- toString(num)， 几乎每个值都有的犯法，num为可选参数（2，8，10，16），指定什么进制的格式返回输出，默认十进制
- String() ，转型函数，在不知道要转换的值是否是 null 或 undefined 的情况下，可用其转换任何类型的值为字符串

**String()转换规则：**

说明 | 返回值
:-|:-
如果值有 toString() 方法 | 则调用该方法返回相应结果
null | "null"
undefined | "undefned"

### Object 类型

ECMAScript中的对象其实就是一组数据和功能的集合。可以通过 new 操作符后跟要创建的对象名称来创建，如：
	
	var myobj = new object();

**Object每个实例都具有的属性和方法：**

属性/方法 | 说明
:-|:-
constructor | 保存着用于创建当前对象的函数，构造函数(constructor)就是object()
hasOwnProperty(propertyName) | 用于检查给定的属性在当前对象实例中是否存在。propertyName必须以字符串形式指定，如：myobj.hasOwnProperty('name')
isPropertyOf(object) | 用于检查传入的对象是否是当前对象的原型
propertyIsEnumerable(propertyName) | 用于检查给定的属性是否能够使用 for-in 语句
toLocalString() | 返回对象的字符串表示，该字符串与执行环境的地区对应
toString() | 返回对象的字符串表示
valueOf() | 返回对象的字符串、数值或布尔值表示，通常与 toStirng() 方法返回值相同




## 变量提升

ES6之前一般使用var来声明变量，提升简单来说就是把我们所写的类似于var a = 123;这样的代码，声明提升到它所在作用域的顶端去执行，到我们代码所在的位置来赋值；在执行代码之前，解析器会先执行代码分析，在这个过程中，会将变量申明提升到作用域的顶端去执行。

如：

	console.log(v1);
	var v1 = 100;
	function foo() {
	    console.log(v1);
	    var v1 = 200;
	    console.log(v1);
	}
	foo();
	console.log(v1);

执行结果：

	//undefined
	//undefined
	//200
	//100

## let 和 const

在ES6以前，JS只有var一种声明方式，但是在ES6之后，就多了let跟const这两种方式。用var定义的变量没有块级作用域的概念，而let跟const则会有，因为这三个关键字创建是不一样的。

区别如下：

	{
	    var a = 10
	    let b = 20
	    const c = 30
	}
	a // 10
	b // Uncaught ReferenceError: b is not defined
	c // c is not defined
	let d = 40
	const e = 50
	d = 60
	d // 60
	e = 70 // VM231:1 Uncaught TypeError: Assignment to constant variable.


说明 | var | let | const
:-:|:-:|:-|:-:
变量提升 | √ | × | ×
全局变量	| √	| ×	| ×
重复声明	| √	| ×	| ×
重新赋值	| √	| √	| ×
暂时死区	| ×	| √	| √
块作用域	| ×	| √	| √
只声明不初始化| √	| √	| ×



