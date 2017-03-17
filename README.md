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
