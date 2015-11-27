---
layout: post_new
title: "Zephir入门 —— Hello world篇"
description: ""
tags: [Zephir,PHP]
published: true
---
# 概述 #
[Zephir](http://zephir-lang.com){:target="_blank"}是一个开源的用于简化PHP扩展的创建和维护的语言。它使得不擅长C/C++的PHP开发人员也能写出PHP扩展。Zephir是Zend Engine/PHP/Intermediate缩写，读音为zephyr。

Zephir在语法上跟PHP有很多相似之处，PHP开发人员可以很快上手，但也有很多地方上的不同需要我们去学习。下面是Zephir一些主要的特色：

| 变量类型 | 动态/静态                  |
| 内存安全 | 不允许指针和直接的内存管理 |
| 编译模型 | AOT(ahead of time)         |
| 内存模型 | task-local垃圾回收         |

# 安装 ##

## 依赖 ##
要使用Zephir和编译出一个PHP扩展，需要先安装以下的依赖：

- gcc >= 4.x/clang >= 3.x
- re2c 0.13+
- gnu make 3.81+
- autoconf 2.31+
- automake 1.14+
- libpcre3
- php development headers and tools

## 安装 ##
这里选择使用`git`的方式获取源代码并进行安装：

~~~ bash
$ git clone https://github.com/phalcon/zephir
$ cd zephir
$ ./install-json
$ ./install -c
~~~

> 如果已经安装了`json-c`，那么可以忽略 `./install-json`这一步。

## 验证 ##
通过运行zephir命令验证下是否安装成功：

~~~ bash
$ zephir help

 _____              __    _
/__  /  ___  ____  / /_  (_)____
  / /  / _ \/ __ \/ __ \/ / ___/
 / /__/  __/ /_/ / / / / / /
/____/\___/ .___/_/ /_/_/_/
         /_/

Zephir version 0.7.1b

Usage: 
        command [options]

Available commands:
        stubs               Generates extension PHP stubs
        install             Installs the extension (requires root password)
        fullclean           Cleans the generated object files in compilation
        build               Generate/Compile/Install a Zephir extension
        generate            Generates C code from the Zephir code
        clean               Cleans the generated object files in compilation
        builddev            Generate/Compile/Install a Zephir extension in development mode
        compile             Compile a Zephir extension
        version             Shows the Zephir version
        api [--theme-path=/path][--output-directory=/path][--theme-options={json}|/path]Generates a HTML API
        help                Displays this help
        init [namespace]    Initializes a Zephir extension

Options:
        -f([a-z0-9\-]+)     Enables compiler optimizations
        -fno-([a-z0-9\-]+)  Disables compiler optimizations
        -w([a-z0-9\-]+)     Turns a warning on
        -W([a-z0-9\-]+)     Turns a warning off
~~~

# 我们的第一个扩展 ##
下面我们使用Zephier来创建一个“hello world”扩展。

## 初始化 ##
首先，我们使用`init`命令来初始化扩展的基本结构（假设我们扩展的名称为“utils”）：

~~~ bash
$ zephir init utils
~~~

成功运行后，我们应该会得到如下的目录结构：

~~~ bash
utils/
   ext/
   utils/
~~~

`ext`目录里放的是编译器需要用到的代码，不用理会，我们的Zephir代码将放在跟扩展名同名的`utils`里。

## 编写代码 ##
我们在`utils`目录下创建一个文件：`greeting.zep`，并编写代码：

~~~ php
namespace Utils;

class Greeting
{

    public static function say()
    {
        echo "hello world!";
    }

}
~~~

这里不深入Zephir的语法，但是可以看到语法跟PHP很类似，上面的代码定义了一个类`Greeting`和一个方法`say()`。

> Zephir的语法详情可以参考官方的文档：<http://zephir-lang.com/language.html>。

## 编译 ##
接下来，我们回到`utils`根目录下并运行`build`命令编译出扩展：

~~~ bash
$ zephir build     
Preparing for PHP compilation...
Preparing configuration file...
Compiling...
Installing...
Extension installed!
Add extension=utils.so to your php.ini
Don't forget to restart your web serverp
~~~

编译成功后，我们在PHP配置文件里增加以下一行：

~~~ ini
extension=utils.so
~~~

## 验证 ##
通过如下命令查看我们的扩展是否正常加载：

~~~ bash
php -m
[PHP Modules]
...
memcached
mysql
mysqli
mysqlnd
openssl
utils
...
~~~

如果看到我们扩展的名字，则证明已成功加载。

然后我们在PHP里调用`say()`方法：

~~~ php
<?php

echo Utils\Greeting::say(), "\n";
~~~

正常的话会输出：`hello world!`。至此我们也完成了我们的第一个扩展。

# 参考 #
<http://zephir-lang.com/index.html>

