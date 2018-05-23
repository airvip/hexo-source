---
title: laravel初学者之路--创建视图、控制器、模型完成注册
date: 2016-05-18 17:07:41
tags:
    - Php
    - Laravel
---

> 死亡过于绝对，而活着，则有无限的可能。

laravel初学者之路将由laravel5及之后的版本作为学习目标，这里只是简单的记录一下学习过程，有兴趣的朋友可以一起学习。正所谓：“三人行，必有我师焉”。

<!-- more -->

# 学习笔记

定义显示注册页面路由
``` php
Route::get('register', function(){
    return view('home.auth.register');
});
```
创建控制器（laravel原有的已被删除）
``` bash
D:\>cd download/apache/htdocs/self>php artisan make:controller Auth/AuthController
```
![create controller](/img/201605/laravel/controller.gif)

创建模型
``` bash
D:\>cd download/apache/htdocs/self>php artisan make:model User
```

编写视图页面（laravel使用blade模版引擎）
关于blade引擎，可以自行学习
``` html
//在(项目/resources/views/)下创建layouts文件夹
//在layouts文件夹下创建master.blade.php视图文件,代码如下
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
    <link rel="stylesheet" href="{{asset('css/font-awesome.css')}}">
    <link rel="stylesheet" href="{{asset('bower_components/bootstrap/dist/css/bootstrap.min.css')}}">
    {{--<link rel="stylesheet" href="{{asset('bower_components/bootstrap/dist/css/bootstrap-theme.min.css')}}">--}}
    {{--<link rel="stylesheet" href="{{asset('css/templatemo-style.css')}}">--}}
    <link rel="stylesheet" href="{{asset('css/main.css')}}">
  
    <title>@yield('title')阿尔维奇的个人主页</title>
</head>
<body>
    @include('layouts.nav')
    <div class="container">
        @yield('content')
    </div>
    <script src="{{asset('bower_components/jquery/dist/jquery.min.js')}}"></script>
    <script src="{{asset('bower_components/bootstrap/dist/js/bootstrap.min.js')}}"></script>
</body>
</html>


//在layouts文件夹下创建nav.blade.php视图文件,代码如下

<nav class="navbar navbar-default navbar-fixed-top">
   <div class="container">
      <div class="navbar-header">
         <a href="{{url('/')}}" class="navbar-brand logo"><b>AirBlog</b></a>
         <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
            <span class="sr-only">&nbsp;</span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
         </button>
      </div>
      <div class="collapse navbar-collapse" id="navbar">
         <ul class="nav navbar-nav navbar-right">
            <li class="dropdown" id="web-user">
               <a href="##" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">
                  <span class="glyphicon glyphicon-user"></span>会员<span class="caret"></span>
               </a>
               <ul class="dropdown-menu">
                  <li><a href="{{url('login')}}">登录</a></li>
                  <li class="divider"></li>
                  <li><a href="{{url('register')}}">注册</a></li>
               </ul>
            </li>
         </ul>
      </div>
   </div>
</nav>

//在(项目/resources/views/)下创建email文件夹
//在email文件夹下创建userActive.blade.php视图文件,代码如下
<!doctype html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
</head>
<body>
<p>尊敬的用户{{$nickname}}，您好：</p>
<p>您使用了邮箱 {{$email}}注册成为【AirBlog】的会员。请点击以下链接，确认您在AirBlog的注册。</p>
<p><a href="{{url('auth/active?token='.$token)}}" target="_blank">{{url('auth/active?token='.$token)}}</a></p>
<p>如果以上链接不能点击，你可以复制网址URL，然后粘贴到浏览器地址栏打开，完成确认。</p>
<p>阿尔维奇</p>
<p>（这是一封自动发送的邮件，请不要直接回复）</p>
<p>说明</p>
<p>如果你没有注册过AirBlog，可能是有人尝试使用你的邮件来注册，请忽略本邮件。</p>
<p>没有激活的账号会为你保留24个小时, 请尽快激活。</p>
<p>24个小时以后, 没有被激活的注册会自动失效，你需要重新填写并注册。</p>
</body>
</html>

//项目/public/css/main.css,其他均为公用css,请自行下载
body{padding-top:55px;}
#reg_login{
    max-width: 300px;
    margin: 30px auto;
}
```

界面如下
![view html](/img/201605/laravel/view.jpg)

编写控制器（文件在：项目/app/http/controllers/auth下）
``` php
//该文件用到了自定义方法tokenPass,用到了模型，用到了Eloquent ORM
//errors/result.blade.php视图请自己写
<?php
namespace App\Http\Controllers\Auth;
use Mail;
use App\User;
use Illuminate\Support\Facades\Input;
use Illuminate\Http\Request;
use Validator;
use App\Http\Controllers\Controller;
use Illuminate\Foundation\Auth\ThrottlesLogins;
use Illuminate\Foundation\Auth\AuthenticatesAndRegistersUsers;
class AuthController extends Controller
{
    public function registerRun(Request $request){
        //add use Illuminate\Http\Request;
        $this->validate($request,[
            'nickname' => 'required|max:20|unique:users',
            'email' => 'required|email|max:100|unique:users',
            'password' => 'required|min:6|confirmed',
        ]);
        $temp       = Input::all();
        //'password' => bcrypt($temp['password']),将会产生60位数据
        $cheack_time    = time()+3600*24;
        $key            = tokenPass($temp['nickname'].'&'.$temp['email'].'&'.$cheack_time);
        $user       = User::create([
            'nickname' => $temp['nickname'],
            'email'    => $temp['email'],
            'password' => md5($temp['password']),
            'remember_token' => $key
        ]);
        $data   = ['nickname'=>$user->nickname,'email'=>$user->email,'token'=>$user->remember_token];
        Mail::send('email.userActive',$data, function ($m) use ($user) {
            $m->to($user->email, $user->nickname)->subject('Pleace active');
        });
        return view('errors.result',['result'=>'success','message'=>'邮件已经发送,请尽快激活...']);
    }

    public function userActive(){
        $token = Input::get('token');
        $user   = explode('&',tokenPass($token,1));
        if($user[2] < time())$this->error('您的激活信息已失效，请重新注册！');//最好配合contab执行
        $userObj   = User::user($user[1])->get()->first();
        if(1 == $userObj->remember_token)return view('errors.result',['result'=>'error','message'=>'您已经激活了该账户...']);
        $userObj->remember_token = 1;
        $userObj->save();
        return view('home.auth.login',['active_mess'=>'激活成功，请登录...']);
    }
}
```

模型文件（文件:项目/app/User.php）
``` php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Foundation\Auth\User as Authenticatable;
class User extends Model
{
    protected $table = 'users';
    protected $fillable = [
        'nickname', 'email', 'password','remember_token',
    ];
    public function scopeUser($query, $email)
    {
        return $query->where('email', $email);
    }
}
```

引入自定义方法（文件：项目/bootstarp/autoload.php添加如下）
``` php
<?php
define('LARAVEL_START', microtime(true));
//引入自定义函数路径
require __DIR__.'/../app/functions.php';
```

自定义函数（文件:项目/app/functions.php）
``` php
<?php
if(!function_exists('tokenPass')){
    function tokenPass($value,$type=0){
        if(0 == $type)return str_replace('/','@',base64_encode($value));
        return base64_decode(str_replace('@','/',$value));
    }
}
```

邮件配置（文件：项目/.env）（文件：项目/config/mail.php）

到此一个带有2天时间限制简单注册完成。