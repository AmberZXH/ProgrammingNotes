 # JavaScript学习笔记

————— 本文档为《JavaScript高级程序设计（第三版）》一书的个人学习笔记，随时更新。

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


> ES6(ES2015) ES6是一次重大的革新，比起过去的版本，改动比较大。

### JavaScript 放置引用

1. 嵌入式调用： <script type=“text/javascript”></script>
2. 可以在超链接或是重定向的位置调用javascript代码格式： "javascript:alert('我是超链接')"重定向格式：action="javascript:alert('我是表单')"
3. 在事件后面进行调用
4. 外部引入： <script src=“js.js” type=“text/javascript”></script>

---

## 基本概念

### 语法

ECMAScript 的语法大量借鉴了 C 及其他类 C 的语言（Java、Perl）的语法。

#### 区分大小写

ECMAScript 中的一切（变量、函数名和操作符），都是严格区分大小写的。 如：test 和 Test 表示的是两个不同的变量。

#### 标识符

**标识符**就是指变量、函数、属性的名字，或者函数的参数。

标识符的规则：

1. 第一个字符必须是字母、下划线 _ 或者 $ 符
2. 其他字符可以是字母、数字、下划线或 $ 符

**按照惯例，ECMAScript标识符采用<u>小驼峰格式</u>。**

#### 注释

ECMAScript 使用 C 的注释风格，包括单行注释和块级注释

	// 这是单行注释

	/*
	 * 这是一个多行注释（块级注释）
	 */


#### 严格模式

ECMAScript 5 引入了严格模式（strict mode）的概念。

要开启严格模式，可以在顶部加入如下代码：

	"use strict";

也可定义函数在严格模式下执行：

	function myfun(){
		"use strict";
		...	
	}

#### 语句

ECMAScript 中的语句以一个分号结尾；如果省略分号，则由解析器确定语句的结尾。如：

	var sum = a + b       // 即使没有分号也有效————不推荐
	var diff = a - b；    // 推荐写法
	

### 关键字和保留字

ECMAScript-262 描述了一组具有特定用途的**关键字**，用于表示控制语句的开始或结束，或执行特定的操作等。

ECMAScript-262 还描述了一组不能用作标志符的**保留字**，尽管还没有任何特定用途，但将来可能被使用。

**使用关键字时会出现"Identifier Expected" 错误，使用保留字可能会出现相同错误，取决于特定的引擎。**


### 变量

ECMAScript 中的变量是**松散类型**的，松散类型就是**可以用来保存任何类型的数据**，变量只是存储数据的一个容器，变量分为**全局变量**和**局部变量**两种。变量的创建，一般以 **var** 关键字来修饰。

**1.先声明，后赋值：**

	var test;
	test = "this is a test";

**2.声明和赋值同时进行：**

	var url="www.baidu.com";

**3.一次声明多个变量，然后再赋值：**

	var name, age;
	name="lisi";
	age=14;

**4.一次声明多个变量同时进行赋值：**

	var name="wangwu",age=18,sex="girl";

#### 声明变量注意的问题

1. **如果重新声明该变量，而没有赋值，该变量的值不会改变**。
2. 如果重新声明该变量并且重新赋值，那么旧的变量值会删除，改为新的变量值。

### 数据类型

ECMAScript 中有**5种简单的数据类型（也称为基本数据）**和**1种复杂数据类型**。

5种简单的数据类型：

- Unidefined
- Null
- Boolean
- Number
- String

1种复杂数据类型：

- Objdect，Object本质上是一组由无序的名值对组成的。

#### typeof 操作符

由于ECMAScript是松散类型的，所谓松散类型就是可以用来保存任何值（未保存初始值的变量会保存一个特殊值——undefined），检测变量的数据类型使用 typeof。

对一个值使用 typeof可能返回下列某个字符串：

- "undefined" —— 如果这个值未定义
- "boolean" —— 如果这个值是布尔值
- "string" —— 如果这个值是字符串
- "number" —— 如果这个值是数值
- "object" —— 如果这个值是对象或**null**
- "function" —— 如果这个值是函数

#### Undefined 类型

Undefined 类型只有一个值，即特殊的undefined。

对于尚未声明过的变量，只能执行一项操作，即使用 typeof 操作检测数据类型。

对未申明的变量调用 delete 不会导致错误，在严格模式下确实会导致错误。


#### Null 类型

Null 类型是第二个只有一个值的数据类型，该值为 null，null值表示一个空对象指针，所以使用 typeof 检测时会返回 “Object”。

	var car = null;
	alert(typeof car);  // "object"

**如果定义的变量准备在将来用于保存对象，那么最好将该变量的初始值设为 null 而不是其他值。**

#### Boolean 类型

Boolean只有两个字面量值： **true** 和 **false**，这两个值与数字值不是一回事，<u>ture 不一定等于 1，而 false 也不一定等于 0</u>，true 和 fasle 是严格区分大小写的（Ture 和 False 等其他大小写混合形式都不是Boolean的值）；

将值转换为对应的 Boolean 值，可使用 Boolean() 函数，如下：

	var mes = "hello world";
	var mesAsBoolean = Boolean(mes); 

**各数据类型及其对应的转换规则：**

数据类型 | 转换为 true 的值 | 转换为 false 的值
:- | :- | :-
Boolean | true | fasle
Sring | 任何非空字符串 | "" （ 空字符串，注意 " " 会转为 true ）
Number | 任何非 0 数字值（包括无穷大） | 0 和 NaN
Object | 任何对象 | null
Undefined | n/a(not applicable，不适用) | undefined

#### Number 类型

##### 浮点数

所谓浮点数值，就是该数值中必须包含一个小数点，并且小数点后面至少有一位数字。

	var flaotNum1 = 1.1;
	var floatNum2 = 0.1;
	var floatNum3 = .1;  // 有效，但不推荐

**注意：**

如果小数点后面没有跟任何数字，那么这个数值可以作为整数值保存。

如果浮点数本身表示的就是一个整数（1.0），那么，也会被转为整数。

	 var floatNum1 = 1.;   // 小数点后没有数字，解析为 1
	 var floatNum2 = 10.0  // 解析为整数

对于极小或极大的数值，可用 e 表示法（科学计数法）表示。用 e 表示法表示的数值等于 e 前面的数值乘以 10 的指数次幂。

	var num1 = 3.125e7;  // (3.12e+7) 等于 31250000
	var num2 = 3e-5;     // 等于 0.00003


##### 数值范围

说明 | 参数 | 取值
:-|:-|:-
最小值 | Number.MIN_VALUE | 5e-324
最大值 | Number.MAX_VALUE | 1.7976931348623157e+508

如果某次计算的结果超出了数值范围，将自动转换为特殊的 **Infinity（正无穷）/ -Infinity（负无穷）**，判断一个值是否是有穷的，使用 **isFinite():**

	var result = Number.MAX_VALUE;
	alert(isFinite(result));  // false

##### NaN

NaN（Not a Number）非数值的一个特殊的数值，判断一个参数是否“不是数值”使用 **isNaN()** ，有两个特点：

- 任何涉及NaN的操作都会返回NaN
- NaN与任何值都不相等，**包括NaN自身**

##### 数值转换

把非数字转换为数值： Number()、 parsetInt()、parsetFloat()

转型函数Number()，可用于任何类型；另外两个专门用于把字符串转为数值。

**Number() 转换规则：**

原始值 | 转换结果说明 
:-|:-
Boolean | true和false转为：1/0
Number | 返回原始值
**null** | **0**
**undefined** | **NaN**
String（只含数字，包括正负号情况） | 十进制数值，忽略前导零，'123' => 123，'011' => 11
String（包含有效浮点格式） | 对应的浮点数值，忽略前导零
String（包含十六进制格式，包括正负号情况） | 相同大小的十进制整数，如：'0xf' => 15
**String（空字符串，不含任何字符）** | **0**
**上述之外的字符** | **NaN**
Object | 调用 valueof() 方法，按照前面的规则转换，如果结果为NaN，调用对象的 toString() 方法，按前面的规则返回

#### String 类型

在JavaScript中，单、双引号的解析效率是一样的。

##### 字符字面量

String 数据类型包含一些特殊的字符字面量，也叫转义序列，用于表示非打印字符或者具有其他用途的字符。**特殊字面量如下：**

字面量 | 含义
:- | :- 
\n | 换行
\t | 制表
\b | 退格
\r | 回车
\f | 进纸
\\\ | 斜杠
\' | 单引号（'）
\" | 双引号（"）
\xnn | 以十六进制代码nn表示的一个字符（其中n为0~F），如： \x41表示 "A"
\unnnn | 以十六进制代码nnnn表示的一个Unicode字符（其中n为0~F），如： \u03a3表示 Σ

##### 转换为字符串

把一个值转换为字符串有两种方式：

- **toString(num)**， 几乎每个值都有的方法，num为可选参数（2，8，10，16），指定以什么进制的格式返回输出，默认十进制
- **String()** ，转型函数，<u>在不知道要转换的值是否是 null 或 undefined 的情况下，可用其转换任何类型的值为字符串</u>

**String()转换规则：**

说明 | 返回值
:-|:-
如果值有 toString() 方法 | 则调用该方法返回相应结果
null | "null"
undefined | "undefined"

#### Object 类型

ECMAScript 中的对象其实就是一组数据和功能的集合。可以通过 new 操作符后跟要创建的对象名称来创建，如：
	
	var myobj = new object();
	var myobj2 = new object;   // 有效，但不推荐省略圆括号

**Object每个实例都具有的属性和方法：**

属性/方法 | 说明
:-|:-
constructor | 保存着用于创建当前对象的函数，构造函数(constructor)就是object()
hasOwnProperty(propertyName) | 用于检查给定的属性在当前对象实例中是否存在。propertyName必须以字符串形式指定，如：myobj.hasOwnProperty('name')
isPropertyOf(object) | 用于检查传入的对象是否是当前对象的原型
propertyIsEnumerable(propertyName) | 用于检查给定的属性是否能够使用 for-in 语句
toLocalString() | 返回对象的字符串表示，该字符串与执行环境的地区对应
toString() | 返回对象的字符串表示
valueOf() | 返回对象的字符串、数值或布尔值表示，通常与 toStirng() 方法返回值相

### 操作符

ECMA-262 描述了一组用于操作数据值的**操作符**，包括以下几类：

1. 算数操作符（如加、减、乘、除）
2. 为操作符
3. 关系操作符
4. 相等操作符

#### 一元操作符

**一元操作符**：只能操作一个值的操作符。主要有：递增和递减操作符、一元加和减操作符。

**1. 递增和递减操作符**

递增和递减操作符各有两个版本：**前置型** 和 **后置型**，即: 前++/前-- 和 后++/后--，前置和后置的区别在于：前置型是**先进行 加 1/减 1，**再进后续操作，而后置型则相反，**先进行运算**之后再进行 加 1/减 1;

	var age = 29;
	++age; // 30 等价于 age = age + 1;

	var age = 29;
	var other = --age+2;
	alert(age);    // 输出 28
	alert(other);  // 输出 30
	
递增和递减操作符遵循下列规则：

应用条件 | 操作
:-|:-
一个包含有效数字的字符串 | 先转为数字值，再执行加减 1，字符串变量变成数值变量
一个不含有效数字的字符串 | 将变量的值变为 NaN，字符串变量变成数值变量
布尔值 true/false | 先转为 1/0，再执行加减 1，布尔值变量变成数值变量
浮点数值 | 执行加减 1 操作
对象 | 先调用对象的 valueOf() 方法，以取得一个可供操作的值，然后对该值进行前面的操作。如果结果为 NaN，则调用 toString()方法后再调用前面的规则

**2. 一元加和减操作符**

一元加和减操作符遵循下列规则：

应用条件 | 操作
:-|:-
数值 | 不会产生任何影响
非数值 | 会像 Number() 转型函数一样对这个值进行转换
布尔值 true/false | 转为 1/0
字符串值 | 会按照一组特殊的规则进行解析
对象 | 先调用对象的 valueOf() 和 toString() 方法，再转换得到的值

**注意：一元减（-）操作符主要用于表示负数，如 1 转换为 -1**

#### 位操作符

位操作符用于最基本的层次上，即按内存中表示数值的位来操作数值。**ECMAScript 中的所有数值都以 IEEE-754 64位格式存储，但位操作并不直接操作64位的值，而是先转为32位的整数，然后操作，最后将结果转换回64位**。

对于有符号的整数，32位中的前31位用于表示整数的值。第32为用于表示数值的符号：0 为正数，1为负数。这个表示符号的位叫做符号位。（详见39~44页）。

名称 | 表示符号 | 规则
:-|:-:|:-
按位非（NOT）| ~ | 返回数值的反码
按位与（AND）| & | **同为 1 为 1**，只有两个数值的对应位都是 1 时才返回 1，任何一位是 0，结果都是 0 
按位或（OR）| 一个竖线符号 | **同为 0 为 0**，只有两个数值的对应位都是 0 时才返回 0，任何一位是 1，结果都是 1 
按位异或（XOR）| ^ | **只有一个 1 返回 1**，只有两个数值的对应位上只有一个 1 时才返回 1，其他情况返回 0
左移| << | 将数值的所有位向左移动指定的位数
有符号的右移| >> | 将数值向右移动，但保留符号位（即正负号标记）
无符号的右移| >>> | 将数值的所有32位都向右移动

#### 布尔操作符

布尔操作符一共有 3 个： 与（AND）、或（OR）、非（NOT）。

**1. 逻辑与**

表示符号：AND / &&，执行结果：**同为真为真**。**逻辑与属于短路操作符**，<u>即如果第一个操作数的结果为 false，那么就不会再对第二个操作数求值</u>。

当有一个操作数不是布尔值时，逻辑与遵循的规则：

条件 | 返回值
:-|:-
第一个操作数是对象 | 返回第二个操作数
第二个操作数是对象 | 当第一个的求值结果为 true 时，才会返回第二个操作数
两个操作数都是对象 | 返回第二个操作数
第一个操作数是 null | 返回 null
第一个操作数是 NaN | 返回 NaN
第一个操作数是 undefined | 返回 undefined

	var found = true;
	var result = (found && someUndefinedVariable);  // 会发生错误
	alert(result);  // 不会执行

上面的代码中，因为 found 的值为 true，所以逻辑与会对第二个变量 someUndefinedVariable 继续求值。由于 someUndefinedVariable 未定义，所以会导致错误。因此，**不要在逻辑与中使用未定义的值，可能导致错误**。
	
	var found = false;
	var result = (found && someUndefinedVariable);  // 不会发生错误
	alert(result);  // false


**2. 逻辑或**

表示符号：OR / ||，执行结果：**同为假为假**。**逻辑或属于短路操作符**，<u>即如果第一个操作数的结果为 true，那么就不会再对第二个操作数求值</u>。

当有一个操作数不是布尔值时，逻辑或遵循的规则：

条件 | 返回值
:-|:-
第一个操作数是对象 | 第一个操作数
第一个操作数求值结果为 false | 第二个操作数
两个操作数都是对象 | 第一个操作数
两个操作数是 null | null
两个操作数是 NaN | NaN
两个操作数是 undefined | undefined

	var found = true;
	var result = (found || someUndefinedVariable);  // 不会发生错误
	alert(result);  // true

上面的代码中，因为 found 的值为 true，所以逻辑与不会对第二个变量 someUndefinedVariable 求值。
	
	var found = false;
	var result = (found && someUndefinedVariable);  // 会发生错误
	alert(result);  // 不会执行

逻辑与的常用模式：
 
	var result = res1 || res2;


**3. 逻辑非**

表示符号：NOT / !，执行结果：**取反**。**无论什么数据类型，都会返回一个布尔值**，会先将操作数转为布尔值，再对其求反。

逻辑非遵循的规则：

条件 | 返回值
:-|:-
操作数是对象 | false
操作数是空字符串 | true
操作数是非空字符串 | false
操作数是任意非 0 数值(包括 Infinity)| false
操作数是 null | true
操作数是 NaN | true
操作数是 undefined | true

**逻辑非也可用于将一个值转为期对应的布尔值，使用 !! 会模拟 Boolean() 转型函数的行为**，如：

	alert(!!'blue');   // true
	alert(!!0);        // false
	alert(!!NaN);      // false
	alert(!!"");       // false
	alert(!!123);      // true

#### 乘性操作符

ECMAScript 定义了 3 个乘性操作符：乘法、除法和求模。

**1. 乘法**

乘法由 * 表示，用于计算两个数值的乘积。

	var result = 20 * 20；   // 400;

乘法操作遵循的特殊规则：

条件 | 操作规则
:-|:-
操作数都是数值 | 执行常规乘法计算，两个正数或负数相乘结果为正，有一个操作数有符号，结果为负数。如果乘积超过了ECMA数值的表示范围，则返回 Infinity 或 -Infinity
有一个操作数是NaN | NaN
Infinity * 0 | NaN
Infinity * 非0 | Infinity 或 -Infinity
有一个数不是数值 | 先调用 Number() 将其转为数值，再应用上面的规则

**2. 除法**

除法由 / 表示，用于计算第二个操作数除第一个操作数的结果。

	var result = 4 / 2；   // 2;

除法操作遵循的特殊规则：

条件 | 操作规则
:-|:-
操作数都是数值 | 执行常规除法计算，两个正数或负数结果为正，有一个操作数有符号，结果为负数。如果计算结果超过了ECMA数值的表示范围，则返回 Infinity 或 -Infinity
有一个操作数是NaN | NaN
Infinity / Infinity | NaN
0 / 0 | NaN
非 0 有限数 / 0 | NaN
Infinity / 非 0 | Infinity 或 -Infinity
有一个数不是数值 | 先调用 Number() 将其转为数值，再应用上面的规则

**3. 求模**

求模由 % 表示，用于获取第二个操作数除第一个操作数的**余数**。

	var result = 3 * 2；   // 1;

求模操作遵循的特殊规则：

条件 | 计算结果
:-|:-
操作数都是数值 | 执行常规除法计算，返回余数
Infinity / 有限大数值 | NaN
Infinity / 0 | NaN
Infinity / Infinity | NaN
有限大数值 / 0 除 | NaN
有限大数值 / Infinity | 被除数
被除数是 0 | 0
有一个数不是数值 | 先调用 Number() 将其转为数值，再应用上面的规则


#### 加性操作符

**1. 加法**

加法操作符用 + 号表示。

如果两个操作符都是数值，则执行常规的加法计算，然后根据下面的的规则返回结果：

条件 | 计算结果
:-|:-
一个操作数是NaN | NaN
Infinity + Infinity | Infinity
-Infinity + -Infinity | -Infinity
-Infinity + Infinity | NaN
+0 + +0 | +0
-0 + -0 | -0
0 + -0 | 0

如果一个操作符都是字符串，则根据下面的的规则返回结果：

条件 | 操作规则
:-|:-
两个操作数都是字符串 | 将第1、2个操作数拼接起来
一个操作数是字符串 | 将另一个操作数转为字符串，然后拼接起来
一个操作符是对象，布尔值或数值 | 调用它们的 toString() 方法，取得相应的字符串值，再应用于前面的规则。**对于 undefined 和 null 则调用 String() 方法，取得 "undefined" 和 "null" 字符串。**


**2. 减法**

减法操作符用 - 号表示

则执行减法计算，需要遵循的特殊规则：

条件 | 计算结果
:-|:-
两个操作数都是数值 | 常规计算方法，返回结果。
一个操作数是NaN | NaN
Infinity - Infinity | NaN
-Infinity - -Infinity | NaN
Infinity - -Infinity | Infinity
+0 - +0 | +0
-0 - +0 | -0
-0 - -0 | +0
一个操作符是字符串、布尔值、undefined 或 null | 调用 Number() 转为数值，再根据前面的规则计算。
一个操作符是对象 | 调用 valueOf() 取得该对象的数值，如果没有 valueOf() 方法，则调用 toString()方法，然后将得到的结果转为数值，再据前面的规则计算。

#### 关系操作符

小于（<）、大于（>）、小于等于（<=）、大于等于（>=），这几个关系操作符用于比较两个值的大小，返回一个布尔值。

比较时的计算规则：

条件 | 计算规则
:-|:-
两个操作数都是数值 | 进行数值比较
两个操作数**都是字符串** | 比较**对应的字符编码值** 
一个操作数是数值 | 将另一个转为数值，再进行数值比较
一个操作数是对象 | 调用对象的 valueOf()，如没有则调用 toString()，将得到的结果按之前的规则进行比较
一个操作数是布尔值 | 先将其转化为数值，再比较

#### 相等操作符

**1. 相等和不相等**

相等（===），不等（！=），ECMAScript 提供了两组操作符： 

- **相等**和**不相等**——先转行再比较
- **全等**和**不全等**——仅仅比较，不转换

比较时遵循的规则：

条件 | 比较规则
:-|:-
一个操作数是布尔值 | 先转为数值 1/0，再比较
一个字符串和一个数值 | 将字符串转为数值，再比较
一个操作数是对象 | 调用 valueOf() 得到基本类型值，再按之前的规则比较
null 和 undefined | 是相等的
一个操作数是 NaN | 相等比较返回 false，不等比较返回 true
两个操作数都是对象 | 如果是同一对象，相等返回 true，不等返回 false 

**需要注意： NaN 不等于 NaN**

**2. 全等和不全等**

全等（===），不全等（！==），**不仅比较大小，还要比较类型是否相等。**

#### 条件操作符

**条件操作符**，即常说的**三元操作符。**

	var result = (1 > 2)? 'Yes' : 'No';
	alert(result);    // No

	var result = (10 > 2)? 'Yes' : 'No';
	alert(result);    // Yes


#### 赋值操作符

简单的赋值操作符由 = 号表示，作用是把右侧的值赋值给左侧的变量。
	
	var num = 10;

在赋值操作符（=）前加上乘性操作符、加性操作符合位操作符可以完成复合赋值：

	var num = 10;
	num += 10;    // 等价于 num = num + 10;

- 乘 / 赋值（*=）
- 除 / 赋值（/=）
- 模 / 赋值（%=）
- 加 / 赋值（+=）
- 减 / 赋值（-=）
- 左移 / 赋值（<<=）
- 有符号右移 / 赋值（>>=）
- 无符号右移 / 赋值（>>>=）

这些操作符的目的是***简化赋值操作**，***不会带来任何性能的提升。**

#### 逗号操作符

逗号操作符可以在一条语句中执行多个操作，如：

	var num = 1, age = 18, name = 'lisi'；

除了用于声明多个变量外，还可以进行赋值，**用于赋值时，逗号操作符总会返回表达式中的最后一项**。如：

	var num = (1,2,3,4,0);   // num 的值为 0


### 语句

ECMAScript 规定了一组语句（也称流程控制语句）。流程即代码的执行顺序，流程控制即通过规定的语句让程序代码有条件的按照一定方法执行。

#### if 语句

	if (条件) {
		...
	    条件成立时执行的语句	
	}
	
	if (条件) {
		...
	    条件成立时执行的语句	
	} else {
		...
	    条件不成立时执行的语句	
	}

	if 条件1) {
		...
	    条件1成立时执行的语句	
	} else if (条件2) {
		...
	    条件2成立时执行的语句	
	} else if (条件3) {
		...
	    条件3成立时执行的语句	
	} else {
		...
	    条件1、2、3都不成立时执行的语句	
	}

#### do-while 语句

do-while 语句是一种**后测试循环语句**，即只有在循环体中的代码执行之后，才会测试出口条件。即：不管条件是否成立，都会**至少先执行一次** do 中的语句，然后再执行 while 中的语句。

语法：

	do {
	    statement
	} while (expression);

示例：

	var i = 0;
    do {
        i += 2;
        console.log(i);
    } while (i < 10 );
    console.log(i);

	// 执行结果
	2
	4
	6
	8
	10
	10

**像 do-while 这种后测试语句一般用于循环体中的代码至少要被执行一次的情形。**

#### while 语句

while 语句是一种**前测试循环语句**，即在循环体中的代码被执行之前，就会对出口条件求值。

语法：

	while (expression) statement;

示例：

	var i = 0;
    while ( i < 10 ) {
        console.log(i);
		i += 2;
	}

	// 执行结果
	0
	2
	4
	6
	8

#### for 语句

for 语句是一种**前测试循环语句**，它具有在执行循环条件之前初始化变量和定义循环后要执行的代码的能力。

语法：

	for (initialization; expression; post-loop-expression) statement;

示例：

    for (var i = 0; i < 10; i++ ) {
        console.log(i);
		i += 2;
	}

	// 执行结果
	0
	3
	6
	9


#### for-in 语句

for-in 语句是一种**精准迭代的语句，可用来枚举对象的属性。**

语法：

	for (property in expression) statement;

示例：

    for (var propName in window) {    // 将 window 对象中的所有属性名赋值给 propName
        console.log(propName);
	}

	// 执行结果，会打印出 window 对象中的所有属性名
	postMessage
	blur
	focus
	close
	...


#### switch 语句

switch 语句和 if 语句比较相似，**推荐在判断某种范围时最好用 if，判断某个值时用 switch **

语法：

	switch (expression) {
        case value: statement
            break;
        case value: statement
            break;
        case value: statement
            break;
        default: statement

示例：

    switch (i) {
        case 2:
            alert('2');
            break;
        case 4:
            alert('4');
            break;
        default: alert('other');
    }
    // 等价于下面 if 的表达
    if(i == 2){
        alert('2');
    }else if(i == 4){
        alert('4');
    }else{
        alert('other');
    }

switch 另一种使用方式：

	var num = 25;
    switch (true) {
        case num < 0:
            alert('小于 0');
            break;
        case num > 0 && num < 10:
            alert('大于 0 小于 10');
            break;
        default: alert('other');
    }


通过为每个 case 后面添加一个 break 语句，就可以避免同时执行多个 case 代码的情况。假如要混合几种情况，不要忘了在代码中添加注释说明。如：

	switch (i) {
        case 2:
            // 合并两种情况
        case 4:
            alert('2 or 4');
            break;
        case 5:
            alert('5');
            break;
        default: alert('other');
    } 

**注意： <u>swicth 在比较值的时候使用的是全等（===）</u>。**

#### break 和 continue 语句

break 和 continue 用于在循环中精确地控制代码的的执行。break 会**退出循环体，强制继续执行循环体之后的代码**， continue 会**退出该层循环，继续执行下一次循环。**
                                                                                                                         
#### label 语句

label 语句可以在代码块中添加标签，以便将来使用。

语法：

	label: statement;

示例：

	var num = 0;
	for (var i = 0 ; i < 10 ; i++){
	     for (var j = 0 ; j < 10 ; j++){
	          if( i == 5 && j == 5 ){
	                break;
	          }
	     num++;
	     }
	}
	alert(num); // 循环在 i 为5，j 为5的时候跳出 j循环，但会继续执行 i 循环，输出 95
 
对比使用了 label 之后的程序：
    var num = 0;
    outPoint:
    for (var i = 0 ; i < 10 ; i++){
         for (var j = 0 ; j < 10 ; j++){
              if( i == 5 && j == 5 ){
                    break outPoint;
              }
         num++;
         }
    }
    alert(num); // 循环在 i 为5，j 为5的时候跳出双循环，返回到outPoint层继续执行，输出 55
 
对比使用了break、continue语句：

	var num = 0; 
	outPoint:  
	for(var i = 0; i < 10; i++) {  
		for(var j = 0; j < 10; j++) {  
			if(i == 5 && j == 5) { 
			continue outPoint;
			}  
			num++;  
		}  
	}  
	alert(num);  //95  

从alert(num)的值可以看出，continue outPoint;语句的作用是跳出当前循环，并跳转到outPoint（标签）下的for循环继续执行。

#### with 语句

with 语句的作用是将代码的作用域设置到一个特定的对象中。

语法：

	with (expression) statement;

定义 with 语句的作用是为了简化多次编写同一个对象的工作，示例：

	var qs = location.search.substrring(1);
	var hostName = location.hostname;
	var url = location.href;
 
以上的代码都包含了 location 对象。如果使用 with 语句，可以把上面的代码改成如下所示：
	
	with (location){
		var qs = location.search.substrring(1);
		var hostName = location.hostname;
		var url = location.href;
	}

以上重写的列子中，使用 with 语句关联了 location 对象。这意味着，在 with 语句的代码块内部，每个变量首先被认为是一个局部变量，而如果在局部变量环境中找不到该变量的定义，就会查询 location 对象中是否有同名属性。如果发现了同名属性，则 location 对象的属性值作为变量的值；

**严格模式下不允许使用 with 语句，会出现语法错误。大量使用 with 语句会导致性能下降，在大型应用开发中不建议使用 with 语句。**



### 函数

将完成某一特定功能的代码集合起来，可以重复使用的代码块。

优点：

- 使程序更加简洁
- 逻辑更有条理
- 调用方便
- 易于维护

#### 函数的创建

**基本语法：**

	function 函数名（[参数1]...）{
		函数体...
		[return] //返回值
	}

**字面量形式（匿名函数）：**

	var 变量 = function（[参数1]...）{
		函数体...
		[return] //返回值
	}

#### 函数的调用

1. 函数名()
2. 通过事件触发调用：对象.事件 = function(){}
3. 自调用：(function (){})()

#### 函数的参数

- 参数的作用:可以动态的改变函数体内对应的变量的值，使同一函数体得
到不同的结果。
- 形参:在定义函数的时候，函数括号中定义的变量叫做形参。用来接受实参的。
- 实参:调用函数的时候，在括号中传入的变量或值叫做实参。用于传递给形参。

**参数的个数：**

- 实参和形参个数**相等**，**一一对应**。
- 实参**小于**形参的个数，**不会报错**，多出的会自动赋值为默认值或 undefined
- 实参**大于**形参的个数，**不会报错**，使用 **arguments 对象来获取多出的实参**。

#### 函数的返回值

**return 语句**

– 通过return 语句给函数一个返回值，停止并且跳出当前的函数

**return语句的用法**

– 在return 语句后面的函数体内所有内容都不会输出。
– 在函数体内可以有多个return语句，但是只会执行一个。(判断语句)

**return语句的返回值**

– 返回值可以是任何的数据类型
– 只能返回一个返回值。
– 如果函数没有返回值，那么这个函数的值就会自动的赋值为 undefined

#### 创建及调用注意事项

1. 如果两个函数名称相同，后面的会覆盖前面的。
2. 以基本语法声明的函数，会在页面载入时提前解析到内存中，以便调用，可以在函数前面调用。以字面量形式命名的函数，会在执行到它的时候，才进行赋值，所以，只能在函数后面调用。
3. 在不同的<script></script>块中，因为浏览器解析的时候是分块解析的，所以前面的块不能调用后面块的函数，所以在不同的块之间调用函数的时候，应该**先定义后调用**。


---


## 作用域和内存问题

### 基本类型和引用类型的值

ECMAScript 中包含**两种不同的数据类型的值**： **基本类型值**和**引用类型值。**

基本类型值是指简单的数据段，而引用类型的值是指那些可能由多个值构成的对象。

**5 种基本数据（Undefined、Null、Boolean、Number、String）类型**是**按值访问的**，可以操作保存在变量中的实际值。

**引用类型的值是保存在内存对象中的**，是**按引用访问的**。在操作对象时，实际是操作对象的引用而不是实际的对象。

不能给基本类型的值添加属性，虽然这样做不会导致任何错误。

#### 复制变量值

在从一个变量向另一个变量复制基本类型值和引用类型值时，存在不同。

一个变量向另一个变量赋值基本类型的值时，会在变量对象上创建一个新的值，然后把该值复制到的变量分配的位置上，**两个变量的值完全独立，互不影响**。

一个变量向另一个变量赋值引用类型的值时，**两个变量实际上引用的是同一个对象，会互相影响**。

#### 传递参数

ECMAScript 中**所有函数的参数都是按值传递**的，把函数外部的值复制给函数内部的参数，就和把值从一个变量复制到另一个变量一样。**基本类型值的传递和基本类型变量的复制一样**，**引用类型值的传递和引用类型变量的复制一样**。

**变量的访问有<u>按值</u>和<u>按引</u>两种方式**，而**参数只能按值传递**。

在向参数传递基本类型的值时，被传递的值会被复制给一个局部变量。

在向参数传递引用类型的值时，会将**被传递的值在内存中的引用地址**复制给一个局部变量，因此这个局部变量的变化会反映在函数的外部。

**可以把 ECMAScript 函数的参数想象成局部变量。**                                                         

#### 检测类型

**1. typeof**

typeof 操作符用于检测一个变量的数据类型。**如果变量的值时 null 或 一个对象，则返回 'object'**

	var str = 'string';
	var num = 'number';
	var nul = null;
	var obj = new object();
	
	alert(typeof str);      // string
	alert(typeof num);      // number
	alert(typeof str);      // object
	alert(typeof str);      // object

**2. instanceof**

instanceof 操作符用于检测一个值是那种引用类型。

### 执行环境及作用域

执行环境（execution context）也称**环境**。执行环境定义了变量或函数有权访问的其他数据，决定了它们的行为。每个执行环境都有一个与之相关联的**变量对象（variable object）**，环境中定义的所有变量和函数都保存在这个对象中。

Web 浏览器中，全局环境被认为是 window 对象。

**每个函数都有自己的执行环境**。

当代码在一个环境执行时，会创建变量对象的一个**作用域链**。作用域链的作用：保证对执行环境有权访问的所有变量和函数的有序访问。

全局执行环境的变量对象始终都是作用域链中的最后一个对象。

在局部作用域中定义的变量可以在局部环境中用于全局环境中互换使用。

示例：

	var color = 'blue';

    function changeColor() {
        var anotherColor = 'red';

        function swapColor() {
            var tempColor = anotherColor;
            anotherColor = color;
            color = tempColor;

            // 这里可以访问 color、anotherColor、 tempColor
        }

        // 这里可以访问 color、anotherColor，但不能访问 tempColor
        swapColor();
    }
    // 这里只能访问 color
    changeColor();

上面的示例中共有 3 个执行环境：全局环境、changeColor() 的局部环境和 swapColor() 的局部环境。全局环境中有一个变量 color 和 一个函数 changeColor()。changeColor()的局部环境中有一个变量 anotherColor 和一个函数 swapColor()，但它可以访问期父级环境（全局环境）中的变量 color。swapColor() 的局部环境有一个 tempColor 变量，它可以访问到其父级和父级的父级中的变量，但是，当前环境中的变量 tempColor 只能在当前的环境中访问。

**函数参数也被当做变量来对待，因此其访问规则与执行环境中的其他变量相同。**


#### 延长作用域链

执行环境的类型只有 2 种——**全局和局部（函数）**。

在使用下列语句时，作用域链会得到加长：

- try-catch 语句的 catch 块
- witch 语句

#### 没有块级作用域

JavaScript 没有块级作用域，在 JavaScript 中，**if 语句中的变量声明会将变量添加到当前的执行环境**，在使用时尤其需要注意。

	if(true){
        var color = 'red';
    }
    alert(color); // red

在 JavaScript 中，**for 语句创建的变量 i 即使在 for 循环执行结束后，也依旧会存在于外部的执行环境中**，在使用时尤其需要注意。

	for(var i = 0; i<10; i++){
	    console.log(i);
	}
	alert(i);  // 10

**1. 声明变量**

使用 var 声明的变量会自动被添加到最接近的环境中。在函数内部，最接近的环境就是函数的局部环境；在 with 语句中，最接近的环境就是函数环境；**如果初<u>始化变量时没有使用 var 声明</u>，该变量会被<u>自动添加到全局环境中</u>。**

在 JavaScript 代码编写过程中，**不声明而直接初始化变量**是一个常见的**错误**，在严格模式下会导致错误。

**2. 查询标识符**

当某个环境中为了读取或写入而引入一个标识符（标识符：指变量、函数、属性的名字，或者函数的参数）时，必须通过搜索来确定该标识符实际代表什么。搜索过程从作用域前端开始，逐级向上查询（**从当前向上一层追溯**），找到就停止，否则一直追溯到全局环境的变量对象，如果在全局环境中也未找到则该变量未声明。

### 垃圾收集

JavaScript 具有自动垃圾收集机制。其**原理： 找出那些不再继续使用的变量，然后释放其占用的内存**。为此，垃圾收集器**会按照固定的时间间隔（或代码之中预定的收集时间），周期性的执行这一操作**。

用于标记无用变量的策略(**垃圾收集策略**)通常有两种：

1. **标记清除（mark-and-sweep）【最常用】**
2. 引用计数（reference counting）

#### 管理内存

一旦数据不再有用，最好通过<u>将其值设为 null 来将其引用释放</u>——这个叫**解除引用（dereferencing）**。这一做法**适合大多数全局变量和全局对象的属性**，**局部变量**会在它们**离开执行环境时自动解除引用。**


## 引用类型

引用类型的值（对象）是**引用类型**的一个实例。**引用类型**是一种数据结构，用于将数据和功能组合在一起，也被称为**类**。虽然引用类型与类看起来相似，但他们并不是相同的概念。对象是某个特定引用类型的**实例**。

### object 类型

创建 object 实例的方式有两种。第一种是使用 new 操作符后跟 object 构造函数，如下所示：

	var person = new object();
	person.name = 'lisi';
	person.age = 18;

另一种是使用**对象字面量**表示方法，如下所示：

	var person = {
		name : 'lisli',
		age : 18
	};

	// 在使用对象字面量语法时，属性名也可使用字符串
	var person = {
		"name" : 'lisi',
		"age" : 18,
		5 : true        // 注意：这里的数值属性名会自动转为字符串
	};

使用对象字面量语法时，如果留空其花括号，则可以定义只包含默认属性和方法的对象，如下所示：

	var person = {};    // 与 new object() 相同
	person.name = 'lisi';
	person.age = 18;

**对象字面量是向函数传递大量可选参数的首选方。**

一般来说，访问对象属性时使用的都是点表示，这是很多面向对象语言中的通用的语法。在 JavaScript 中也可使用方括号表示法来访问对象的属性。使用示例：

	alert(person.name);            // "lisi"
	alert(person["name"]);         // "lisi"

上面的例子中，两种方法访问对象属性的方法没有任何区别。但是，**方括号语法的主要优点是可以通过变量名来访问属性**，例如：

	var personName = 'name';
	alert(person[personName]);    // "lisi"

**如果属性名中包含会导致语法错误的字符，或者使用了关键字或保留字，可以使用方括号表示法**，例如：
	
	person["first name"] = "lisi";

**除非必须使用变量来访问属性，否则我们<u>建议使用点表示法</u>。**

---

## 面向对象编程


---


## 变量提升

ES6之前一般使用var来声明变量，变量提升简单来说就是把我们所写的类似于var a = 123;这样的代码，声明提升到它所在作用域的顶端去执行，到我们代码所在的位置来赋值；在执行代码之前，解析器会先执行代码分析，在这个过程中，会将变量申明提升到作用域的顶端去执行。

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
:-:|:-:|:-:|:-:
变量提升 | √ | × | ×
全局变量	| √	| ×	| ×
重复声明	| √	| ×	| ×
重新赋值	| √	| √	| ×
暂时死区	| ×	| √	| √
块作用域	| ×	| √	| √
只声明不初始化| √	| √	| ×



