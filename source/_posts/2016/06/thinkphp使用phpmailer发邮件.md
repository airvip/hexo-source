---
title: thinkphp使用phpmailer发邮件
date: 2016-06-01 23:24:23
tags:
    - Thinkphp
    - Phpmailer
---

> 败者死于绝望，胜者死于渴望。

PHPMailer是一个用于发送电子邮件的PHP函数包。

<!-- more -->

很多使用 email 注册用户信息的项目中都有验证邮箱真实性的功能，在 php 中我们可以使用 phpmailer 非常简便的发送我们定制的邮件信息。

# 实战操作

第一步，下载 phpmailer
[在sourceforge中下载phpmailer的入口](https://sourceforge.net/projects/phpmailer/)
[在github中下载phpmail的入口](https://github.com/PHPMailer/PHPMailer)

第二步：
把下载的文件放在如下目录：`thinkphp/library/vendor/`下面，并按命名规则命名
![phpmailer_dir](/img/201606/phpmailer/phpmailer_dir.jpg)
可以看到PHPMailer里面有一个PHPMailerAutoload.php，该文件是自动载入phpmail所需的类库，所以我们直接引入该文件就好了。

第三步:

在应用目录里面任意模块的控制器文件夹建立文件，我之所以会在`Application/Common/Controller`文件夹下建立`EmailController.class.php`,是因为我要在前台后台都调用该控制器，实现代码的重用。
![phpmailer_file](/img/201606/phpmailer/phpmailer_file.jpg)

EmailController.class.php的代码如下，如果您放的目录不一样，记得修改命名空间

``` Php
<?php
namespace Common\Controller;
use Think\Controller;

class EmailController extends Controller{
    public function _initialize(){
        header("Content-Type:text/html; charset=utf-8");
    }

    //利用phpmail发送邮件
    public function send_mail($to, $title, $content) {
        Vendor('PHPMailer.PHPMailerAutoload');
        $mail           = new \PHPMailer();         //实例化
        $mail->IsSMTP();                            // 启用SMTP
        $mail->Host     = 'smtp.qq.com';            //smtp服务器的名称（'smtp.163.com'）
        $mail->Port     = 25;                       //smtp服务器的端口
        $mail->Timeout  = 20;                       //超时时间
        $mail->SMTPDebug  = 0;                      //smtp debug（测试3商用0）
        $mail->SMTPAuth = 'true';                   //启用smtp认证
        $mail->Username = '980062449@qq.com';       //你的邮箱名
        $mail->From     = '980062449@qq.com';       //发件人地址（也就是你的邮箱地址）
        $mail->FromName = '阿尔维奇';                //发件人姓名
        $mail->Password = 'sdhfkhsfdkhui';          //邮箱密码(QQ的要发短信获取，163登录密码)
        $mail->addAddress($to, '阿尔维奇');
        $mail->WordWrap = 500;                      //设置每行字符换行长度
        $mail->isHTML(true);                         // 是否HTML格式邮件
        $mail->CharSet  = 'UTF-8';                   //设置邮件编码
        $mail->Subject  = $title;                    //邮件主题
        $mail->Body     = $content;                  //邮件内容
        $mail->AltBody  = "这是一封来自Airblog的慰问邮件"; //Message body does not support HTML standby display.
        return($mail->Send());
    }
}

?>
```

大家可能会有疑问，这样如何调用?其实很简单,我们用THINKPHP的A()方法就好了，下面是调用代码
```
 //发送邮件
$to         = $data['user_email'];
$title      = '感谢您注册Air个人博客';
$content    = '<p>没有激活的账号会为你保留24个小时, 请尽快激活。</p>';
$content    .= '<p>24个小时以后, 没有被激活的注册会自动失效，你需要重新填写并注册。</p>';
$email_rs   = A('Common/Email')->send_mail($to,$title,$content);
if(false == $email_rs)$this->error('激活邮件发送失败...');
```

亲测，163与QQ完美发送邮件。