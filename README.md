### PHP学习笔记
#### ___**Day 1**
##### 自定义网站根目录方法
第一步：更改Apache的httpd.conf文件的两处   

- 1、DocumentRoot "X:/wamp\www" 中"X:/wamp\www"文件夹地址改为你新建的文件夹地址，例如"d:/phpDocument"
- 2、下面几行```<Directory "X:/wamp\www"> ```中的"X:/wamp\www"文件夹地址亦改为你新建的文件夹地址，例如"d:/phpDocument"  

第二步：左击菜单栏“www 目录”显示的更改，需要更改wamp开发包中的配置文件   

- 1、更改wampmanager.ini文件中[Menu.Left]标记中Type: item; Caption: "www 目录"; Action: shellexecute; FileName: "X:/wamp/www"; 这一句中的Caption值"www 目录"为"Demo目录"，并更改FileName值"X:/wamp/www"为目标文件夹，例如："d:/phpDocument"即可。
- 2、更改wampmanager.tpl文件中[Menu.Left]标记中Type: item; Caption: "${w_wwwDirectory}"; Action: shellexecute; FileName: "${wwwDir}";这一句中的Caption值 "${w_wwwDirectory}"为 "Demo目录",更改FileName值"${wwwDir}"为"d:/phpDocument"。
- 3、退出并重新启动所有服务即可。

##### WAMPServer多站点配置方法
第一步：增加站点    

- 打开 wamp\bin\apache\apache2.4.9\conf\extra\httpd-vhosts.conf
- 添加以下代码
```
<VirtualHost *:80>
    DocumentRoot "d:/phpDocument/test01"
    ServerName test01.com
</VirtualHost>
```
第二步：引入httpd-hosts文件    

- 因为上面的conf文件是扩展文件，默认下不会加载
-  打开Apache->httpd.conf文件，找到virtual hosts 
- #Include conf/extra/httpd-vhosts.conf 将Include前的'#'去掉。

第三步：允许站点访问服务器资源   

- 打开Apache->httpd.conf文件，找到onlineoffline tag,修改其后面的Deny from all为Allow from all，同时将Allow from 127.0.0.1修改为注释（前面加‘#’），在2.5版本中，无需进行此操作，默认为Require all granted。

第四步：为站点添加资源       
  
- 打开```d:/phpDocument``` 新建test01文件夹，随意创建一个index.php进行测试（里面添加点内容）

第五步：设置本机访问这些站点时从本机获取资源   

- 打开c:/windows/system32/drivers/etc/hosts
- 在最后添加```127.0.0.1 test01.com```
- 打开浏览器 访问 test01.com

##### 自拟端口号（默认80端口被占用的情况）
- wamp环境安装好后启动不了多为端口冲突
- 打开Apache的配置文件httpd.conf，搜索80
- #Listen 12.34.56.78:90   Listen 80改为Listen 8080,再找到ServerName localhost:80改为ServerName localhost:8080,重启服务
- 测试的时候需输入localhost:8080（80是默认的所以只用输入localhost）

#### small-note
- 连接符号是点'.'
- 声明变量前面加$符号，相当于js的var
- var_dump() 输出数据类型
- 当表示字符串的双引号中包含变量时，变量会被解析
- 使用Heredoc结构形式输入很长的字符串，代码如下
```
<?php
	$string = <<<GOD
	我有一只小毛驴，我从来也不骑。   
	有一天我心血来潮，骑着去赶集。   
	我手里拿着小皮鞭，我心里正得意。   
	不知怎么哗啦啦啦啦，我摔了一身泥.   
	GOD;
	echo $string;
	//头尾包含相同的英文，并且首行英文前面加‘>>>’
?>
```
- 自定义常量函数 define(常量名，常量值，是否支持大小写);
- 获取常量使用 defined(常量名)      [加d]
- '&':赋值运算符，引用赋值，意味着两个变量都指向同一个数据。它将使两个变量共享一块内存，如果这个内存存储的数据变了，那么两个变量的值都会发生变化。
```
<?php 
    $a = "111";
	$b = $a;
	$c = &$a;
	$a = "222";
	
	echo $b."<br />";   //111
	echo $c."<br />";   //222
?>
```
- foreach循环语句
1、只取值，不取下标
```
<?php
 foreach (数组 as 值){
//执行的任务
}
?>
```
2、同时取下标和值
```
<?php
foreach (数组 as 下标 => 值){
 //执行的任务
}
?>
```
- str_replace( '被替换内容' , '替换内容' , 替换的字符串对象 ) : 字符串替换方法
- function_exists() , method_exists() , class_exists()  判断是否存在某个对象

#### ___**Day 2**
#### 类和面向对象
- 定义一个类并实例化
```
class Car {
	//定义公共属性
	public $name = "大咪";
	
	//定义受保护的属性
    protected $corlor = '白色';
    
    //定义私有属性
    private $price = '100000';

	//受保护的属性与私有属性不允许外部调用，在类的成员方法内部是可以调用的。
	public function getPrice() {
        return $this->price; //内部访问私有属性
​    }
};
$car = new Car();
echo $car->name  //大咪
echo $car->color;  //错误 受保护的属性不允许外部调用
echo $car->price;  //错误 私有属性不允许外部调用
```

- 类的方法
方法就是在类中的function，同属性一样，类的方法也具有public，protected 以及 private 的访问控制。
```
//定义方法
class Car {
    public function getName() {
        return '汽车';
    }
}
$car = new Car();   //得先实例化对象
echo $car->getName();
```
- 构造函数和析构函数
PHP5可以在类中使用*__construct()*定义一个构造函数，具有构造函数的类，会在每次对象创建的时候调用该函数，因此常用来在对象创建的时候进行一些初始化工作。   

在子类中如果定义了__construct则不会调用父类的__construct，如果需要同时调用父类的构造函数，需要使用parent::__construct()显式的调用。
```
class Car {
   function __construct() {
       print "父类构造函数被调用\n";
   }
}
class Truck extends Car {
   function __construct() {
       print "子类构造函数被调用\n";
       parent::__construct();
   }
}
$car = new Truck();  //子类构造函数被调用父类构造函数被调用
```
同样，PHP5支持析构函数，使用__destruct()进行定义，析构函数指的是当某个对象的所有引用被删除，或者对象被显式的销毁时会执行的函数。

- 静态关键字static 静态方法
使用关键字*static*修饰的，称之为静态方法，静态方法不需要实例化对象，可以通过类名直接调用，操作符为双冒号::。     
静态方法中，$this伪变量不允许使用。可以使用*self*，*parent*，*static*在内部调用静态方法与属性。
```
class Car {
    private static $speed = 10;
    public static function getSpeed() {
        return self::$speed;
    }
}
echo Car::getSpeed();  //调用静态方法 不需要实例化对象 
```

#### ___**Day 3**
- 获取字符串的长度 strlen($str) , 中文字符串的长度用mb_strlen($str) 
- 字符串截取 substr(str , begin , end) , 中文截取mb_substr(str , begin , end)
- 查找字符串的位置 strpos(要处理的字符串 , 要定位的字符串 ，定位的起始位置[可选，默认0])

#### ___**Day 4**
- 替换字符串 str_replace(要查找的字符串, 要替换的字符串, 被搜索的字符串, 替换进行计数[可选])
- 字符串的合并与分割 
1、字符串合并 implode(分隔符，数组)
2、字符串分割 emplode(分隔符，字符串)
- 字符转义函数 addslashes($str)   //防止sql注入
- 正则匹配 
```
$p = '/apple/';
$str = 'apple pen';
preg_match( $p , $str )   //返回true                                                                                                                                             
```
- 设置cookie
setcookie( name , value , expire , path , domain);
后面三个参数可选
name:cookie名 可以通过$_COOKIE['name'] 进行访问
value:cookie值
expire:（过期时间）Unix时间戳格式，默认为0，表示浏览器关闭即失效(一个小时 time()+3600)
path:（有效路径）如果路径设置为'/'，则整个网站都有效
domain:（有效域）默认整个域名都有效，如果设置了'www.imooc.com',则只在www子域中有效
```
<?php
$value = time();
//在这里设置一个名为test的Cookie
setcookie("test",$value,time()+3600,'/','imooc.com');
if (isset($_COOKIE['test'])) {
    echo 'success';
}
```
- 删除cookie
```
setcookie('test', '', time()-1); // 设置time()-1就行了
```
- 使用session
```
//开启一个session
session_start();
//设置键值
$_SESSION['name'] = key;
//也支持任意数据类型
$_SESSION['ary'] = array('name' => 'job');
//打印出这个session
var_dump($_SESSION);
```
- 删除与销毁session
删除某个session值可以使用PHP的unset函数，删除后就会从全局变量$_SESSION中去除，无法访问。
```
session_start();
$_SESSION['name'] = 'jobs';
unset($_SESSION['name']);
echo $_SESSION['name']; //提示name不存在
```
如果要删除所有的session，可以使用session_destroy函数销毁当前session，session_destroy会删除所有数据，但是session_id仍然存在。
```
session_start();
$_SESSION['name'] = 'jobs';
$_SESSION['time'] = time();
session_destroy();
```
值得注意的是，session_destroy并不会立即的销毁全局变量$_SESSION中的值，只有当下次再访问的时候，$_SESSION才为空，因此如果需要立即销毁$_SESSION，可以使用unset函数。
```
session_start();
$_SESSION['name'] = 'jobs';
$_SESSION['time'] = time();
unset($_SESSION);
session_destroy(); 
var_dump($_SESSION); //此时已为空
```
- 使用session来存储用户登录信息
一般来说，登录信息既可以存储在sessioin中，也可以存储在cookie中，他们之间的差别在于session可以方便的存取多种数据类型，而cookie只支持字符串类型，同时对于一些安全性比较高的数据，cookie需要进行格式化与加密存储，而session存储在服务端则安全性较高。
```
//加密与解密
<?php
session_start();
//假设用户登录成功获得了以下用户数据
$userinfo = array(
    'uid'  => 10000,
    'name' => 'spark',
    'email' => 'spark@imooc.com',
    'sex'  => 'man',
    'age'  => '18'
);
header("content-type:text/html; charset=utf-8");

/* 将用户信息保存到session中 */
$_SESSION['uid'] = $userinfo['uid'];
$_SESSION['name'] = $userinfo['name'];
$_SESSION['userinfo'] = $userinfo;

//* 将用户数据保存到cookie中的一个简单方法 */
$secureKey = 'imooc'; //加密密钥
$str = serialize($userinfo); //将用户信息序列化
//用户信息加密前
$str = base64_encode(mcrypt_encrypt(MCRYPT_RIJNDAEL_256, md5($secureKey), $str, MCRYPT_MODE_ECB));
//用户信息加密后
//将加密后的用户数据存储到cookie中
setcookie('userinfo', $str);

//当需要使用时进行解密
$str = mcrypt_decrypt(MCRYPT_RIJNDAEL_256, md5($secureKey), base64_decode($str), MCRYPT_MODE_ECB);
$uinfo = unserialize($str);
echo "解密后的用户信息：<br>";
print_r($uinfo);
```
- 读取文件内容
```
$content = file_get_contents('./test.txt');
```
- 判断文件是否存在
```
file_exists()
```
- 获取当前的Unix时间戳 time() [即从1970年1月1日至今 结果是一串数字]
- 取得当前日期 date(时间戳的格式, 规定时间戳【默认是当前的日期和时间，可选】)
时间戳格式：y-m-d
- 取得日期的Unix时间戳 strtotime('2014-04-29')
