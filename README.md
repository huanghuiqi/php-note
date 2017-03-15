###PHP学习笔记
####**Day 1**
####自定义网站根目录方法
第一步：更改Apache的httpd.conf文件的两处   

- 1、DocumentRoot "X:/wamp\www" 中"X:/wamp\www"文件夹地址改为你新建的文件夹地址，例如"d:/phpDocument"
- 2、下面几行```<Directory "X:/wamp\www"> ```中的"X:/wamp\www"文件夹地址亦改为你新建的文件夹地址，例如"d:/phpDocument"  

第二步：左击菜单栏“www 目录”显示的更改，需要更改wamp开发包中的配置文件   

- 1、更改wampmanager.ini文件中[Menu.Left]标记中Type: item; Caption: "www 目录"; Action: shellexecute; FileName: "X:/wamp/www"; 这一句中的Caption值"www 目录"为"Demo目录"，并更改FileName值"X:/wamp/www"为目标文件夹，例如："d:/phpDocument"即可。
- 2、更改wampmanager.tpl文件中[Menu.Left]标记中Type: item; Caption: "${w_wwwDirectory}"; Action: shellexecute; FileName: "${wwwDir}";这一句中的Caption值 "${w_wwwDirectory}"为 "Demo目录",更改FileName值"${wwwDir}"为"d:/phpDocument"。
- 3、退出并重新启动所有服务即可。

#####WAMPServer多站点配置方法
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

####自拟端口号（默认80端口被占用的情况）
- wamp环境安装好后启动不了多为端口冲突
- 打开Apache的配置文件httpd.conf，搜索80
- #Listen 12.34.56.78:90   Listen 80改为Listen 8080,再找到ServerName localhost:80改为ServerName localhost:8080,重启服务
- 测试的时候需输入localhost:8080（80是默认的所以只用输入localhost）

