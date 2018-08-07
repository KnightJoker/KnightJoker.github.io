title: Ubuntu搭建LAMP环境

date: 2016/03/16 08:46:25

categories:
- Linux
tags:
- LAMP

---

LAMP=Linux+Apache+Mysql+Php 是指搭建动态网站或者服务器的开源软件，四个软件彼此都是独立，但是因为常被放在一起使用，拥有了越来越高的兼容度，共同组成了一个强大的Web应用程序平台


### 步骤1  　 安装Apache

`sudo apt-get install apache2`


#### 验证Apache的安装

 
打开浏览器，输入localhost，如出现It works！便成功安装～

### 步骤2 　安装PHP

`sudo apt-get install php5`


#### 验证PHP的安装


在/var/www/html目录下，创建testphp.php文件，其内容为：

```
<?php
    phpinfo();
?>
```

这里要注意进行文件权限的修改：`sudo chmod 777 /var/www/html`

然后重启服务器：` sudo service apache2 restart`

再在浏览器里输入输入`http://localhost/testphp.php`，当看见php信息后，到此php也安装完毕～

### 步骤3　  安装Mysql和PHPmyadmin

#### 安装Mysql

`sudo apt-get install mysql-server mysql-client`

#### 安装PHPmyadmin

`sudo apt-get install phpmyadmin`


（注意：再进行Mysql安装的时候，提示你讲输入两次Mysql的密码，以及安装过程中会要求选择Webserver：apache2或lighttpd，选择apache2，按tab键然后确定就行）

#### 验证Mysql和PHPmyadmin的安装

将phpmyadmin与apache2建立连接，`sudo ln -s /usr/share/phpmyadmin /var/www`

然后直接在浏览器打开`http://localhost/phpmyadmin`，看有没有数据库管理软件出现。

修改testphp.php代码如下：

```
<?php
    $link = mysql_connect("localhost", "root", "password");
         if(!$link)
              die('Could not connect: ' . mysql_error());
          else
               echo "Mysql 配置正确!";
    mysql_close($link);
?>
```

访问 `http://localhost/testphp.php `显示’Mysql 配置正确‘就代表配置正确。