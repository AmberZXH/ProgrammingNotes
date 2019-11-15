<font size=6>**PHP 错误处理**</font>

# PHP错误分类
 
## 语法错误

由于 PHP 语法有误导致的错误，语法错误最常见，并且也容易修复。如：代码中少了一个分号等。**这类错误会阻止脚本的执行。**

## 运行时的错误

这类错误一般不会阻止 PHP 脚本的执行，但会阻止当前要做的事情。输出一条错误，**但 PHP 脚本会继续执行。**

## 逻辑错误

这种错误最麻烦，**既不阻止脚本执行，也不输出错误类型。**


# PHP 错误级别

PHP 错误级别共分为 3 大类：注意级别、警告级别、严重错误级别。

级别常量 | 错误值 | 描述
:-|:-|:-
E_ERROR | 1 | 致命的运行错误（会阻止脚本运行）
E_WARNING | 2 | 运行时警告（非致命错误） 
E_PARSE | 4 | 语法解析错误
E_NOTICE | 8 | 运行时的注意消息（可能是一个问题）
E_CORE_ERROR | 16 | PHP 启动时初始化过程中的致命错误
E_CORE_WARNING | 32 | PHP 启动时初始化过程中的警告（非致命性错误）
E_COMPILE_ERROR | 64 | 编译时致命性错误
E_COMPILE_WARNING | 128 | 编译时致命错误
E_USER_ERROR | 256 | 用户自定义的致命错误
E_USER_WARNIGN | 1024 | 用户自定义的警告（非致命性错误）
E_STRICT | 2048 | 编码标准化警告（建议如何修改以向前兼容）
E_ALL | 6143 | 所有的错误、警告和注意信息不包含E_STRICT

# 设置报告级别

设置错误报告级别有两种方法：修改 php.ini配置文件、在 PHP 脚本中只用 error_reporting()函数【推荐】。

## 在 php.ini 中修改

在 php.ini 中修改 error_reporting 配置项的值，修改成功后 重启服务器。在设置 error_reporting的值时可以使用位运算符【&（与）、|（或）、~（非）】和错误常量一起使用，如下所示：


	// 抛出人格非注意的错误，默认值
	error_reporting=E_ALL & ~E_NOTICE
	// 只抛出警告和致命错误
	error_repoting=E_ERROR|E_WARNING

## 使用 error_reporting()函数

在 PHP 脚本中使用 error_reporting() 函数，基于各个脚本来调整，如：

	error_reporting(0)      // 完全关闭错误报告
	error_reporting(E_ALL)  // 抛出所有发生的错误
	error_reporting(E_ALL & ~E_NOTICE)  // 抛出任何非注意的错误报告
