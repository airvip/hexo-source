---
title: laravel初学者之路--laravel路由学习
date: 2016-05-17 21:47:51
tags:
    - Php
    - Laravel
---

> 人总是会变的，不要害怕改变哦。

laravel初学者之路将由laravel5及之后的版本作为学习目标，这里只是简单的记录一下学习过程，有兴趣的朋友可以一起学习。正所谓：“三人行，必有我师焉”。

<!-- more -->

首先明确一件事情，我们对laravel路由的操作,主要是操作routes.php这个文件

`项目/app/Http/routes.php`

## 基础路由

1.1 最基础的Laravel路由接收一个URI和一个闭包：

例子如下(显示文件均为没有修改配置文件的前提下)
``` php
//get 可以替换为[post|put|delete]
Route::get('/', function () {
    return 'www.dear521.com';
});
//显示视图的路由
Route::get('/', function () {
    //显示文件：项目\resources\views\index.blade.php
    return view('index');
});
Route::get('/', function () {
    //显示文件：项目\resources\views\home\index.blade.php
    return view('home.index');
});
Route::get('/', function () {
    //显示文件：项目\resources\views\home\product\index.blade.php
    return view('home.product.index');
});
//通过路由进入控制器
Route::post('/', 'IndexController@index');
Route::post('/', 'Home\IndexController@index');
```

1.2 为多个动作注册路由

有时候需要一个路由来响应多个不同的HTTP动作，可以使用[ match | any ]方法来实现：
``` php
Route::match(['get', 'post'], '/', function () {
    return 'www.dear521.com';
});
Route::any('user', function () {
    return 'www.dear521.com';
});
```

## 带参路由

2.1 必须带参
``` php
Route::get('user/{id}', function ($id) {
    return 'User '.$id;
});
```

2.2 可选参数
``` php
Route::get('user/{name?}', function ($name = 'wzb') {
    return $name;
});
```

2.3 正则约束
``` php
Route::get('user/{name}', function ($name) {
    //
})->where('name', '[A-Za-z]+');
```

## 命名路由

命名路由使生成URLs或者重定向到指定路由变得很方便，在定义路由时指定路由名称，然后使用数组键as指定路由别名
``` php
Route::get('user/profile', ['as' => 'profile', function () {
    //
}]);
//还可以为控制器动作指定路由名称
Route::get('user/profile', [
    'as' => 'profile', 'uses' => 'UserController@showProfile'
]);
```

## 路由群组

路由群组允许我们在多个路由中共享路由属性，比如中间件和命名空间等，这样的话一大波共享属性的路由就不必再各自定义这些属性。共享属性以数组的形式被作为第一个参数传递到Route::group方法中。

4.1 中间件

要分配中间件给群组中所有路由，可以在群组属性数组中使用middleware键。中间件将会按照数组中定义的顺序依次执行
``` php
Route::group(['middleware' => 'auth'], function () {
    Route::get('/', function ()    {
        // Uses Auth Middleware
    });
    Route::get('user/profile', function () {
        // Uses Auth Middleware
    });
});
```

4.2 命名空间

另一个通用的例子是路由群组分配同一个PHP命名空间给多个控制器，可以在群组属性数组中使用namespace参数来指定群组中控制器的命名空间
``` php
Route::group(['namespace' => 'Admin'], function(){
    // Controllers Within The "App\Http\Controllers\Admin" Namespace

    Route::group(['namespace' => 'User'], function()
    {
        // Controllers Within The "App\Http\Controllers\Admin\User" Namespace
    });});
```

4.3 子域名路由

路由群组还可以被用于子域名路由通配符，子域名可以像URIs一样被分配给路由参数，从而允许捕获子域名的部分用于路由或者控制器，子域名可以通过群组属性数组中的domain键来指定
``` php
Route::group(['domain' => '{account}.myapp.com'], function () {
    Route::get('user/{id}', function ($account, $id) {
        //
    });
});
```

4.4 路由前缀

群组数组属性prefix可以用来为群组中每个给定URI添加一个前缀，比如，你想要为所有路由URIs前面添加前缀admin
``` php
Route::group(['prefix' => 'admin'], function () {
    Route::get('users', function ()    {
        // 匹配 "/admin/users" URL
    });
});
```