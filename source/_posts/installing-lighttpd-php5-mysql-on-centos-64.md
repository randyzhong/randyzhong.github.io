---
title: CentOS 6.4 安装 Lighttpd+PHP5(PHP-FPM)+MySQL
tags:
  - CentOS
  - Lighttpd
  - MySQL
  - PHP
categories:
  - '*Nix'
date: 2013-04-28 18:17:41
---

#### 1. 安装 MySQL 5.0
```bash
yum install mysql mysql-server
```
将 MySQL 服务设置成自动启动
```bash
chkconfig --levels 23 mysqld on #我这里没有 X，所以 23 运行级别就可以了
/etc/init.d/mysqld start #启动 mysqld 服务
```
接下来要设置 MySQL 的 root 密码
```bash
mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MySQL to secure it, we'll need the current
password for the root user.  If you've just installed MySQL, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
Enter current password for root (enter for none):
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MySQL
root user without the proper authorisation.

Set root password? [Y/n] y
New password:
Aborting!

Cleaning up...
[root@m2ahkvpn01 tmp]# mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MySQL to secure it, we'll need the current
password for the root user.  If you've just installed MySQL, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MySQL
root user without the proper authorisation.

Set root password? [Y/n] Y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!

By default, a MySQL installation has an anonymous user, allowing anyone
to log into MySQL without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n]
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n]
 ... Success!

By default, MySQL comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n]
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n]
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MySQL
installation should now be secure.

Thanks for using MySQL!
```
####  2. 安装 Lighttpd

Lighttpd并没有在 CentOS 6 的官方软件库里面，所以我们需要手动安装第三方软件库 Remi RPM + EPEL
```bash
# EPEL库
rpm --import https://fedoraproject.org/static/0608B895.txt
wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
rpm -ivh epel-release-6-8.noarch.rpm

# Remi库
rpm --import http://rpms.famillecollet.com/RPM-GPG-KEY-remi
rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
# yum 软件库调整
yum install yum-priorities
```
接下来要编辑两个文件 /etc/yum.repos.d/epel.repo 和 /etc/yum.repos.d/remi.repo，添加
`priority=10`
```bash
#/etc/yum.repos.d/epel.repo
[epel]
name=Extra Packages for Enterprise Linux 6 - $basearch
#baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&amp;arch=$basearch
failovermethod=priority
enabled=1
priority=10
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
[...]
```
```bash
#/etc/yum.repos.d/remi.repo
[remi]
name=Les RPM de remi pour Enterprise Linux $releasever - $basearch
#baseurl=http://rpms.famillecollet.com/enterprise/$releasever/remi/$basearch/
mirrorlist=http://rpms.famillecollet.com/enterprise/$releasever/remi/mirror
enabled=1
priority=10
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
failovermethod=priority

[remi-test]
name=Les RPM de remi en test pour Enterprise Linux $releasever - $basearch
#baseurl=http://rpms.famillecollet.com/enterprise/$releasever/test/$basearch/
mirrorlist=http://rpms.famillecollet.com/enterprise/$releasever/test/mirror
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
```
安装 Lighttpd
```bash
yum install lighttpd
```
接下来设置开机启动 lighttpd 并立即启动
```bash
chkconfig --levels 23 lighttpd on #我这里没有 X，所以 23 运行级别就可以了
/etc/init.d/lighttpd start #启动 lighttpd 服务</pre>
这个时候你有可能会碰到这样的错误
```
<span style="color: #0000ff">Starting lighttpd: 2013-04-28 16:57:47: (network.c.260) warning: please use server.use-ipv6 only for hostnames, not without server.bind / empty address; your config will break if the kernel default for IPV6_V6ONLY changes</span>

<span style="color: #0000ff">2013-04-28 16:57:47: (server.c.915) can't have more connections than fds/2: 1024 1024</span>

第一个是 IPV6 的问题，编辑 `/etc/lighttpd/lighttpd.conf` 文件
```apacheconf
[...]
##
## Use IPv6?
##
server.use-ipv6 = "disable"
[...]
```
第二个报错，编辑 `/etc/lighttpd/lighttpd.conf` 文件，注释掉 `server.max-fds = 2048`
```apacheconf
## With SELinux enabled, this is denied by default and needs to be allowed
## by running the following once : setsebool -P httpd_setrlimit on
server.max-fds = 2048
```
或者
```apacheconf
setsebool -P httpd_setrlimit 1
```
这个是 CentOS 自带的 SELinux 的问题，参见[Bug 571924 - lighttpd's server.max-fds conflicts with SELinux policy](https://bugzilla.redhat.com/show_bug.cgi?id=571924 "Bug 571924 - lighttpd")

当然，你也可以用这个命令来暂时禁掉 SELinux
`setenforce 0`
然后再运行 `/etc/init.d/lighttpd start` 就没有问题了

#### 3. 安装PHP5 (PHP-FPM)

我们需要 Lighttpd 通过加载 PHP-FPM 来解析PHP
```bash
yum install php-fpm lighttpd-fastcgi</pre>
```
#### 4. 配置 Lighttpd 和 PHP

编辑 `/etc/php-fpm.d/www.conf`，把其中 user 和 group 设定的用户从 apache 改成 lighttpd
```apacheconf
[...]
; Unix user/group of processes
; Note: The user is mandatory. If the group is not set, the default user's group
;       will be used.
; RPM: apache Choosed to be able to access some dir as httpd
user = lighttpd
; RPM: Keep a group allowed to write in log dir.
group = lighttpd
[...]
```
设置开机启动 php-fpm 并立即启动
```bash
chkconfig --levels 23 php-fpm on #我这里没有 X，所以 23 运行级别就可以了
/etc/init.d/php-fpm start #启动 mysqld 服务
```
首先我们打开`/etc/php.ini`并在文件尾加入 `cgi.fix_pathinfo = 1`。
然后打开 /etc/lighttpd/modules.conf  文件并在server.modules块中取消 <span style="color: #ff0000">include "conf.d/fastcgi.conf"</span> 的注释（即删除前面的“#”）：
```apacheconf
##
## FastCGI (mod_fastcgi)
##
include "conf.d/fastcgi.conf"
```
接着在 `/etc/lighttpd/conf.d/fastcgi.conf` ，找到`fastcgi.server` 块，改成如下：
```apacheconf
fastcgi.server += ( ".php" =&gt;
        ((
                "socket" =&gt; "/tmp/php5-fpm.sock",
                "broken-scriptfilename" =&gt; "enable"
        ))
)
```
修改完成之后重启 lighttpd 服务以使其生效
```bash
/etc/init.d/lighttpd restart
```

#### 5. 测试 PHP
```bash
vi /var/www/lighttpd/info.php
```
```php
<php phpinfo();
?>
```

####  6. 让 PHP5 支持 MySQL
```bash
yum install php-mysql php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc
yum install php-pecl-apc # APC 是类似 eAccelerator 和 Xcache 的php加速组件
/etc/init.d/php-fpm reload #重新加载 php-fpm
```
#### 7.让 PHP-FPM 以 Unix Socket 方式运行
```bash
vi /etc/php-fpm.d/www.conf
```
```apacheconf
[...]
;listen = 127.0.0.1:9000
listen = /tmp/php5-fpm.sock
[...]
```
Reload PHP-FPM

`/etc/init.d/php-fpm reload`