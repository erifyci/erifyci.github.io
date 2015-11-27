---
layout: post_new
title: "Zephir入门 —— 语法篇"
description: ""
tags: [Zephir,PHP]
published: true
---
# 概述 #
Zephir的语法跟PHP很相似，所以这里不会把官网的文档照搬过来翻译一遍，而是会把一些Zephir相较于PHP比较特别的语法挑出来讲一下。如果想要要完整学习Zephir的语法，没有比官网的[文档](http://zephir-lang.com/language.html)更好的地方了。

# 基本语法 #
Zephir的文件后缀名为`zep`，每个文件都必须包含且只能包含一个类。每个类必须有一个命名空间，且目录结构必须跟类名和命名空间匹配。例如下面的目录结构：

~~~
mylibrary/
        router/
                exception.zep # MyLibrary\Router\Exception
        router.zep # MyLibrary\Router
~~~

那么`exception.zep`的命名空间和类名如下：

~~~ php
namespace MyLibrary\Router;

class Exception extends \Exception
{

}
~~~

Zephir里的变量名不需要像PHP那样带`$`符号，且有两种声明的方式：

~~~
// 可以使用var来定义变量，或者指定具体的变量类型
var a;
int b;
bool c = true;

// 可以在一行里声明多个变量
var a = "hello", b = 0, c;
~~~

要修改变量，属性或者数组的值，需要使用`let`关键字。

~~~
let name = "Tony";           // 变量
let this->name = "Tony";     // 对象属性
let data["name"] = "Tony";   // 数组
let self::_name = "Tony";    // 静态变量
~~~

Zephir不支持全局变量，也不允许访问用户域的全局变量，但是可以访问PHP的超级全局变量：

~~~
var price = _POST["price"];

var requestMethod = _SERVER["REQUEST_METHOD"];
~~~

# 变量类型 #
Zehpir支持动态和静态类型。

## 动态类型 ##
动态类型变量跟PHP一样，可以在赋值后修改为不同类型的值。动态类型变量必须用`var`来声明。

~~~
var a, b, c;

// 初始化变量
let a = "hello", b = false;

// 改变它们的值
let a = 10, b = "140";

// 对它们进行操作
let c = a + b;
~~~

Zephir不会检查整型的溢出，如果要操作比较大的数字，可以使用浮点型或者静态型变量'unsigned long'：

~~~
unsigned long my_number = 2147483648;
~~~

Zephir不支持在字符串里使用变量的方式，你可以使用拼接方式：

~~~
var name = "peter";

echo "hello: " . name;
~~~

数组的定义要用中括号`[]`，且字符串型的键需要使用双引号：

~~~
let myArray = [1, 2, 3];
let myHash = [0: "first", 1: true, 2: null];
let myHash = ["first": 7.0, "second": "some string", "third": false];
~~~

## 静态变量 ##
静态变量一旦声明类型后就不能再改变。虽然失去了动态型变量的灵活性，但是在编译时静态变量能进行更多的优化。

Zephir支持以下的静态类型：

|==================|================================================================================|
| 类型             | 描述                                                                           |
|==================|================================================================================|
| boolean          | A boolean expresses a truth value. It can be either ‘true’ or ‘false’.         |
| integer          | Signed integers. At least 16 bits in size.                                     |
| unsigned integer | Unsigned integers. At least 16 bits in size.                                   |
| char             | Smallest addressable unit of the machine that can contain basic character set. |
| unsigned char    | Same size as char, but guaranteed to be unsigned.                              |
| long             | Long signed integer type. At least 32 bits in size.                            |
| unsigned long    | Same as long, but unsigned.                                                    |
| float/double     | Double precision floating-point type. The size is platform-dependent.          |
| string           | A string is series of characters, where a character is the same as a byte.     |
| unsigned long    | Same as long, but unsigned.                                                    |
| array            | An structure that can be used as hash, map, dictionary, collection, stack, etc.|
|==================|================================================================================|

布尔型，非布尔值会自动转化为true或者false：

~~~
boolean a;

let a = true,
    a = 100, // 自动转换为true
    a = null, // 自动转换为false
    a = "hello"; // 抛出编译器异常
~~~

整型：

~~~
int a;

let a = 50,
    a = -70,
    a = 100.25, // 自动转换为100
    a = null, // 自动转换为0
    a = false, // 自动转换为0
    a = "hello"; // 抛出编译器异常
~~~

无符号整型，带符号整型的值会自动转换为无符号。另外要注意的是，无符号整型的最大值要比普通整型大一倍，所以在把无符号整型的值赋予普通整型时要注意下：

~~~
uint a, int b;

let a = 50,
    a = -70, // 自动转换为70

let a = 2147483648,
    b = a; // 可能会丢失数据
~~~

长整型/无符号长整型变量的值范围是普通整型/无符号整型的两倍，除此以外跟普通整型/无符号整型一致。

~~~
ulong a, long b;

let a = 4294967296,
    b = a; // 可能会丢失数据
~~~

字符型：

~~~
har ch, string name = "peter";

let ch = name[2]; // 存储't'
let ch = 'Z'; // 需要用单引号
~~~

字符串：

~~~
string a;

let a = "",
    a = "hello", // 使用用双引号
    a = 'A', // 自动转换为"A"
    a = null; // 自动转换为""
~~~

> 更详细的类型语法请看这里：<http://zephir-lang.com/types.html>

# 操作符 #
Zephir支持的运算符基本跟PHP一致：

~~~
// 算术运算符
// Zephir支持PHP中除了`$a ** $b`（PHP 5.6才开始支持）这种方式外的算术运算符。
let c = a + b;

// 比较运算符
// Zephir支持的比较运算符跟PHP一致（除了PHP 7增加支持的两个比较运算符外）。
if a == b {
    return 0;
} 

// 逻辑运算符
// Zephir支持`&&`、`||`和`!`这三个逻辑运算符，但不支持PHP里`And`、`Or`和`Xor`这三个逻辑运算符。
if a && b || !c {
    return -1;
}

// 位运算符
// 跟PHP一致
if a & SOME_FLAG {
    echo "has some flag";
}

// 三元运算符
// 跟PHP一致
let b = a == 1 ? "x" : "y"; 
~~~

然后，Zephir还支持`empty`、`isset`等特殊的运算符：

~~~
// empty
// 如果表达式的值为null、空字符串或者空数组，则为empty
let someVar = "";
if empty someVar {
    echo "is empty!";
}

// isset
// 用于检查数组的索引或者对象的属性是否已定义
// 功能更类似于PHP中的array_key_exists方法
let someArray = ["a": 1, "b": 2, "c": 3];
if isset someArray["b"] {
    echo "yes, it has an index 'b'\n";
}

// fetch
// 用于简化在PHP中以下的操作：
if (isset($myArray[$key])) {
    $value = $myArray[$key];
    echo $value;
}
// 使用fetch可以用以下代码实现，只有在key存在的情况下，fetch才返回true：
if fetch value, myArray[key] {
    echo value;
}

// typeof
// 用于检查变量的类型
if (typeof str == "string") {
    echo str;
}
~~~

> 更详细的操作符语法请看这里：<http://zephir-lang.com/operators.html>

# 数组 #
数组可以使用关键字`var`和`array`进行声明：

~~~
var a = []; // 类型可以改变
array b = []; // 运行过程中，类型不可以改变
~~~

数组使用中括号进行创建，操作方式跟PHP一样：

~~~
let elements = [];

let elements = [1, 3, 4];

let elements = ["first", 2, true];

let elements[0] = "bar";

let elements = ["foo": "bar", "bar": "foo"];

let foo = elements["foo"];
~~~

> 更详细的数组操作语法请看这里：<http://zephir-lang.com/arrays.html>

# 类和对象 #

## 类 ##
前面说过，每个zephir文件都必须包含一个类，而且只能包含一个类。

Zehpir的类支持`final`和`abstract`两个修饰符，修饰符的作用跟PHP中的一致，例如：

~~~
namespace Test;

/**
 * 这个类不能被扩展
 */
final class MyClass
{

}
~~~

## 方法 ##

类的成员方法支持的可见性修饰符也跟PHP一致，支持`public`、`protected`和`private`，但Zephir强制方法必须显式的带上修饰符。

同时方法还支持`final`和`deprecated`两个修饰符：

~~~
// 这个方法不能被重写
public final function foo()
{   
}

// 调用此方法会抛出E_DEPRECATED 异常
public deprecated function foo()
{  
}
~~~

方法的参数也支持PHP中可选参数的方式，同时也支持静态类型：

~~~
public function doSum(a, b = 3)
{
    return a + b;
}

public function doSum2(int a = 4, int b = 2)
{
    return a + b;
}
~~~

使用静态类型参数时，如果传入的参数类型不一致，Zephir会试图对传入值进行类型的转换。如果基于某些考虑不想Zephir自动去做这个事情，我们可以通过添加一个`!`号禁止这个行为：

~~~
public function filterText(string! text, boolean escape=false)
{
    //...
}
~~~

另外我们还能通过`const`关键字设置参数为只读：

~~~
// a为只读
public function getSomeData(const string a)
{
    // 这里会抛出编译错误
    let a = "hello";
}
~~~

Zephir还提供了方法返回类型的提示功能，使得编译器可以知道方法返回的类型：

~~~
namespace App;

class MyClass
{
    public function getSomeData() -> string
    {
        // 这里会抛出编译异常。因为返回值是布尔型，跟期待的返回类型不一致
        return false;
    }

    public function getSomeOther() -> <App\MyInterface>
    {
        // 这里会抛出编译异常，如果返回的对象不是App\MyInterface接口的一个实现
        return new App\MyObject;
    }

    public function process()
    {
        var myObject;

        // 类型提示会告诉编译器myObject是App\MyInterface的一个实例
        let myObject = this->getSomeOther();

        // 编译器会检查App\MyInterface是否实现了一个叫someMethod的方法
        echo myObject->someMethod();
    }
    
    // 一个方法可以有多个的返回类型，使用|进行分隔
    public function getSomeData2(a) -> string | bool
    {
        if a == false {
            return false;
        }
        return "error";
    }

    // 如果返回类型为void，则表示此方法不允许返回任何数据
    public function setConnection(connection) -> void
    {
        let this->_connection = connection;
    }

}
~~~

## 属性 ##
类的属性同样支持`public`、`protected`和`private`三个修饰符，而且必须显式指定：

~~~
namespace Test;

class MyClass
{

    public myProperty1;
    protected myProperty2;
    private myProperty3;

    // 属性可以赋予默认值，但是这个值必须在编译的时候就能知道而不需要依赖于运行环境
    protected myProperty4 = 5;
    protected myProperty5 = "my value";

    public function setMyProperty1(var myProperty)
    {
        // 修改属性的值
        let this->myProperty1 = myProperty;
    }

    public function getMyProperty1()
    {
        // 读取属性的值
        return this->myProperty1;
    }

}
~~~

Zephir还提供了一个快捷的方式去实现Getter和Setter，例如下面的代码：

~~~
namespace App;

class MyClass
{
    protected myProperty {
        set, get, toString
    };

    protected someProperty = 10 {
        set, get
    };

}
~~~

相当于：

~~~
namespace Test;

class MyClass
{
    protected myProperty;

    protected someProperty = 10;

    public function setMyProperty(myProperty)
    {
        this->myProperty = myProperty;
    }

    public function getMyProperty()
    {
        return this->myProperty;
    }

    public function setSomeProperty(someProperty)
    {
        this->someProperty = someProperty;
    }

    public function getSomeProperty()
    {
        return this->someProperty;
    }

    public function __toString()
    {
        return this->myProperty;
    }

 }
~~~

另外常量的使用也是支持的：

~~~
namespace Test;

class MyClass
{

    const MYCONSTANT1 = false;
    const MYCONSTANT2 = 1.0;

    public function someMethod()
    {
        return MyClass::MYCONSTANT1;
    }
}
~~~

> 更详细的类和对象的使用方式请看这里：<http://zephir-lang.com/oop.html>

# 内置方法 #
Zephir自身也内置了不少的方法，这些方法基本都是PHP中某些方法的OO实现。例如：

~~~
public function foo(string! s)
{
    return strlen(s);
}

// 使用内置方法
public function foo(string! s)
{
    return s->length();
}
~~~

> 更详细的内置方法使用方式请看这里：<http://zephir-lang.com/builtin-methods.html>

# 流程控制 #

## 条件语句 ##

条件判断语句中，`if`和`switch`语句跟PHP类似，但需要注意的是，条件表达式的括号可以省略：

~~~
if a > 100 {
    echo "to big";
} elseif a < 0 {
    echo "to small";
} else {
    echo "ok";
}

switch count(items) {
}
~~~

## 循环语句 ##

循环语句中，用`while`、`loop`和`for`三种语句。

`while`语句跟PHP差不多，`loop`为Zephir增加的语句，用于创建一个无限的循环：

~~~
let n = 40;
loop {
    let n -= 2;
    if n % 5 == 0 { break; }
    echo x, "\n";
}
~~~

`for`的用法跟PHP有很大不同，例如：

~~~
// 循环数组
for item in ["a", "b", "c", "d"] {
    echo item, "\n";
}

let items = ["a": 1, "b": 2, "c": 3, "d": 4];

for key, value in items {
    echo key, " ", value, "\n";
}

// 遍历字符串
string language = "zephir"; char ch;

for ch in language {
    echo "[", ch ,"]";
}
~~~

三种循环语句中均支持`break`语句和`continue`语句。

## 文件包含 ##

Zephir中也提供了类似PHP中require语句的语句：

~~~
if file_exists(path) {
    require path;
}
~~~

注意这个语句只能用于包含PHP文件，而不能包含zephir文件。

> 更详细的流程控制语法请看这里：<http://zephir-lang.com/control.html>

# 异常处理 #

异常处理跟PHP也类似：

~~~
try {

    // 可以在这里抛出异常
    throw new \Exception("This is an exception");

} catch \Exception, e {

    // 异常处理
    echo e->getMessage();
}
~~~

如果不需要的话，保存异常信息的变量`e`可以省略：

~~~
try {

    throw new \Exception("This is an exception");

} catch \Exception {

    echo e->getMessage();
}
~~~

同时捕捉不同类型的异常：

~~~
try {

    throw new \Exception("This is an exception");

} catch RuntimeException|Exception, e {

    echo e->getMessage();
}
~~~

> 更详细的异常处理的使用方式请看这里：<http://zephir-lang.com/exceptions.html>

# 调用PHP函数 #
可以直接调用PHP中内置的函数，例如调用`base64_encode`函数：

~~~
namespace MyLibrary;

class Encoder
{

    public function encode(var text)
    {
        if strlen(text) != 0 {
            return base64_encode(text);
        }
        return false;
    }
}
~~~

用户自己定义的函数也可以调用：

~~~
namespace MyLibrary;

class Encoder
{

    public function encode(var text)
    {
        if strlen(text) != 0 {
            if function_exists("my_custom_encoder") {
                return my_custom_encoder(text);
            } else {
                return base64_encode(text);
            }
        }
        return false;
    }
}
~~~

由于PHP的函数只接收和返回动态变量，所以如果你传入静态变量的话，Zephir会隐式的创建一个临时变量用于函数的调用。而返回的结果如果要赋值给静态变量的话，需要作适当的类型转换：

~~~
namespace MyLibrary;

class Encoder
{

    public function encode(string text)
    {
        string encoded = "";

        if strlen(text) != 0 {
            let encoded = (string) base64_encode(text);
            return '(' . encoded . ')';
        }
        return false;
    }
}
~~~

最后，如果我们需要动态的对函数进行调用，可以使用以下的方式：

~~~
namespace MyLibrary;

class Encoder
{

    public function encode(var callback, string text)
    {
        return {callback}(text);
    }
}
~~~

> 更详细的函数的调用方法请看这里：<http://zephir-lang.com/functions.html>

# 闭包 #
Zephir里也支持闭包和匿名函数，你可以把它返回给PHP：

~~~
namespace MyLibrary;

class Functional
{

    public function map(array! data)
    {
        return function(number) {
            return number * number;
        };
    }
}
~~~

也可以直接调用：

~~~
namespace MyLibrary;

class Functional
{

    public function map(array! data)
    {
        return data->map(function(number) {
            return number * number;
        });
    }
}
~~~

另外Zephir还提供了一个短语法用来定义闭包：

~~~
namespace MyLibrary;

class Functional
{

    public function map(array! data)
    {
        return number => number * number;
    }
}
~~~

# 参考 #
<http://zephir-lang.com/index.html>