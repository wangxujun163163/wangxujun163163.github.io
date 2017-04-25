---
title: Laravel/Lumen中使用跨域功能cors
categories: Laravel
date: 2017-04-25 13:24:03
tags:
 - Laravel
 - cors
---
# 前言
目前我所知道的两种跨域方式，一种是JSONP，一种是CORS

# JSONP原理
JSONP，也就是JSON with Padding的意思，这是一种利用script标签可以跨域的漏洞实现的数据交互协议，但是这种协议是非官方的，下面先看看实际上是怎么实现数据交互的。

首先，我们先在html中预定义一个回调函数：
```
function sayhello(data){
   alert(data.message);
}
```
之后，操作dom元素，动态添加一个script元素，这也是我们实现跨域的最重要的部分。

```
var scriptElement= document.createElement("script");
scriptElement.src = "http://www.example.com/test?callback=sayhello";
document.getElementsByTagName("HEAD")[0].appendChild(scriptElement);
```

在html中动态添加一个script元素后，浏览器会发起一个GET请求，地址是 http://www. example.com/test? callback=sayhello 。 

服务器收到请求后，将需要返回的JSON数据使用回调方法的形式包裹成一个js脚本，方法名也就是上面传入callback查询参数值，这里是sayhello。

sayhello({message:'hello'});

所以，返回如上的js脚本给浏览器，浏览器会立即执行这段代码，我们也就可以通过回调函数回去到数据了，果然程序员们的智慧是无穷的: )。

JSONP的缺点就是只支持GET请求，不支持诸如POST，PATCH,DELETE等请求。


# CORS
简单的说就是服务端通过对HTTP响应增加响应头来实现，需要服务器和浏览器都支持才行。
## 安装

安装依赖包 `barryvdh/laravel-cors`  :
```sh
$ composer require barryvdh/laravel-cors
```

修改文件 `config/app.php`  添加 Cors\ServiceProvider :
```php
Barryvdh\Cors\ServiceProvider::class,
```

## 全局使用

 修改文件  `app/Http/Kernel.php` class 添加CORS中间件支持:

```php
protected $middleware = [
    // ...
    \Barryvdh\Cors\HandleCors::class,
];
```

## 中间件组

修改路由组 支持CORS

```php
protected $middlewareGroups = [
    'web' => [
       // ...
    ],

    'api' => [
        // ...
        \Barryvdh\Cors\HandleCors::class,
    ],
];
```

## 修改cors配置

默认的配置文件在 `config/cors.php`. 你可以使用下面的命令把它拷贝到自己的配置文件目录下:
```sh
$ php artisan vendor:publish --provider="Barryvdh\Cors\ServiceProvider"
```
> **提示:**如果使用了自定义头, 像 `X-Auth-Token` or `X-Requested-With`, 你必须设置 `allowedHeaders` 包含这些头. 或者设置成 `array('*')` 容许所有的自定义头.



    
```php
return [
     /*
     |--------------------------------------------------------------------------
     | Laravel CORS
     |--------------------------------------------------------------------------
     |
     | allowedOrigins, allowedHeaders and allowedMethods can be set to array('*')
     | to accept any value.
     |
     */
    'supportsCredentials' => false,
    'allowedOrigins' => ['*'],
    'allowedHeaders' => ['Content-Type', 'X-Requested-With'],
    'allowedMethods' => ['*'], // ex: ['GET', 'POST', 'PUT',  'DELETE']
    'exposedHeaders' => [],
    'maxAge' => 0,
]
```

`allowedOrigins`, `allowedHeaders` and `allowedMethods` 如果设置成 `array('*')` 表示其他任何网站都可以访问.




