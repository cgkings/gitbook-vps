# Debian 10 搭建caddy2+php7.4+MySQL8.0+WordPress网站

因为网站迁移遇到了一些问题，把解决的过程记录下来，脑子记忆力越来越差了，只能靠笔记了。

开始之前有必要清理一下环境

```
apt update
apt upgrade -y
apt autoremove
#安装curl
apt install curl -y
```

### Caddy2的安装与配置

安装很简单，依次输入如下命令就可以了

```
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo apt-key add -
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee -a etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

查看caddy是否正常运行

```
service caddy status
#正常信息返回
● caddy.service - Caddy
   Loaded: loaded (/lib/systemd/system/caddy.service; enabled; vendor preset: en
   Active: active (running) since Sun 2021-03-21 14:35:20 CST; 5 days ago
     Docs: https://caddyserver.com/docs/
 Main PID: 32056 (caddy)
    Tasks: 8 (limit: 4915)
   Memory: 12.2M
      CPU: 52.242s
   CGroup: system.slice/caddy.service
           └─32056 usr/bin/caddy run --environ --config etc/caddy/Caddyfile

Mar 26 23:20:39 lance-aliyun caddy[32056]: {"level":"info","ts":1616772039.02432
Mar 26 23:25:44 lance-aliyun caddy[32056]: {"level":"error","ts":1616772344.8614
Mar 27 05:25:48 lance-aliyun caddy[32056]: {"level":"info","ts":1616793948.07342
Mar 27 05:25:49 lance-aliyun caddy[32056]: {"level":"error","ts":1616793949.1478
Mar 27 05:25:49 lance-aliyun caddy[32056]: {"level":"error","ts":1616793949.1484

```

浏览器输入服务器IP地址能正常打开caddy文档页，说明安装成功了。

caddy配置前需要准备域名和dns解析设置好，域名问题这里就不细说了，相信大家都会。caddy的配置文件名是Caddyfile，Debian系统默认路径是/etc/caddy/Caddyfile，首字母C大写。caddy性能比Nginx稍微差点，这点差异对于个人微博网站来说可以忽略，caddy的配置简单对于初学者来说最大的优势。具体参考caddy官网文档

```
nano etc/caddy/Caddyfile
#返回配置文件内容
# The Caddyfile is an easy way to configure your Caddy web server.
#
# Unless the file starts with a global options block, the first
# uncommented line is always the address of your site.
#
# To use your own domain name (with automatic HTTPS), first make
# sure your domain's A/AAAA DNS records are properly pointed to
# this machine's public IP, then replace the line below with your
# domain name.
# :80

# Set this path to your site's directory.
# root * usr/share/caddy
# root * home/wwwroot/weixinRSSxml

# Enable the static file server.
# file_server

# Another common task is to set up a reverse proxy:
# reverse_proxy localhost:8080

# Or serve a PHP site through php-fpm:
# php_fastcgi localhost:9000

# Refer to the Caddy docs for more information:
# https://caddyserver.com/docs/caddyfile

# 闲作坊网站
你的域名 {
  encode gzip
  root * home/wwwroot/网站目录 
  file_server
  php_fastcgi 127.0.0.1:9000 #PHP7.4-fpm配置一致
}

```

如上修改好后，退出保存。重启caddy服务

```
service caddy restart #重启
service caddy status  #查看状态
```

一起正常的话，caddy web服务搭建完成。

### MySQL8.0的安装与数据库创建

#### 安装MySQL

```
wget https://repo.mysql.com//mysql-apt-config_0.8.13-1_all.deb
dpkg -i mysql-apt-config_0.8.13-1_all.deb 
rm -rf mysql-apt-config_0.8.13-1_all.deb
apt update && apt upgrade -y
apt install mysql-server -y
```

#### 创建数据库和用户

```
mysql -uroot -p“安装mysql-server设置root密码” #不需要“”
# 登录MySQL之后，使用如下命令：
>create database 你的数据库名称;
>use 你的数据库名称;
>create user 'admin'@'%' identified by '密码';
>grant all privileges on 你的数据库名称.* to 'admin'@'%';
>flush privileges;
>exit;
# 退出数据库后，重启数据库
service mysql restart
```

```
service mysql status
#正常返回信息
● mysql.service - MySQL Community Server
   Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2021-03-19 17:19:27 CST; 1 weeks 0 days ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
 Main PID: 1717 (mysqld)
   Status: "Server is operational"
    Tasks: 38 (limit: 4915)
   Memory: 328.5M
      CPU: 9min 24.320s
   CGroup: system.slice/mysql.service
           └─1717 usr/sbin/mysqld

Mar 19 17:19:26 lance-aliyun systemd[1]: Starting MySQL Community Server...
Mar 19 17:19:27 lance-aliyun systemd[1]: Started MySQL Community Server.
```

### php7.4的安装与配置

#### PHP7.4安装

```
apt-get -y install apt-transport-https lsb-release ca-certificates curl
wget -O etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > etc/apt/sources.list.d/php.list
apt-get update
sudo apt install php7.4
```

安装完后会显示运行 Apache 服务失败，这是正常情况，因为我们先安装并运行了caddy，caddy占用了80端口，导致 Apache 服务运行失败。需要禁止Apache2的开机启动，避免和caddy冲突。

```
service apache2 stop
systemctl disable apache2-service
```

安装PHP必要包

```
apt install php7.4-fpm php7.4-cgi php7.4-curl php7.4-gd php7.4-xml php7.4-xmlrpc php7.4-mysql php7.4-bz2
```

```
service php7.4-fpm status # 查看PHP状态
```

#### 修改PHP配置

添加 listen = 127.0.0.1:9000 和Caddyfile配置一致。

```
nano etc/php/7.4/fpm/pool.d/www.conf
#找到如下需要修改的代码段
; Unix user/group of processes
; Note: The user is mandatory. If the group is not set, the default user's group
;       will be used.
user = www-data
group = www-data

; The address on which to accept FastCGI requests.
; Valid syntaxes are:
;   'ip.add.re.ss:port'    - to listen on a TCP socket to a specific IPv4 address on
;                            a specific port;
;   '[ip:6:addr:ess]:port' - to listen on a TCP socket to a specific IPv6 address on
;                            a specific port;
;   'port'                 - to listen on a TCP socket to all addresses
;                            (IPv6 and IPv4-mapped) on a specific port;
;   '/path/to/unix/socket' - to listen on a unix socket.
; Note: This value is mandatory.
;listen = run/php/php7.3-fpm.sock #注释掉
listen = 127.0.0.1:9000 #新添加

; Set listen(2) backlog.
; Default Value: 511 (-1 on FreeBSD and OpenBSD)
;listen.backlog = 511
```

修改上传文件大小限制配置

```
nano etc/php/7.4/fpm/php.ini
#找到如下需要修改的代码段
;;;;;;;;;;;;;;;;
; File Uploads ;
;;;;;;;;;;;;;;;;
file_uploads = On
upload_max_filesize = 1000M
max_file_uploads = 200
;;;;;;;;;;;;;;;;;;
; Fopen wrappers ;
;;;;;;;;;;;;;;;;;;
default_socket_timeout = 600
;;;;;;;;;;;;;;;;;
; Data Handling ;
;;;;;;;;;;;;;;;;;
post_max_size = 100M
```

```
nano /etc/php/7.3/fpm/php-fpm.conf
#找到如下需要修改的代码段
process_control_timeout = 100
process.max = 128
```

重启PHP，查看PHP状态

```
service php7.4-fpm restart
service php7.4-fpm status
```

### WordPress的安装与配置

安装就是把wordpress源文件解压放到网站目录，依次完成以下步骤。

```
mkdir -p /home/wwwroot/网站目录 #创建网站目录
cd /home/wwwroot/网站目录/ && wget https://wordpress.org/latest.zip #进入网站目录并下载最新版WordPress
apt install unzip -y && unzip latest.zip #解压源文件
mv wordpress/* . #移动所有文件到网站目录
rm -rf latest.zip wordpress  #删除源文件压缩包和解压目录
```

授权网站目录www-data

```
chown -R www-data:www-data /home/wwwroot/网站目录
```

### phpMyAdmin安装

phpMyAdmin是MySQL数据库web界面管理，安装后便于数据库日常维护、备份、导入、导出。网站迁移方便。

```
# wget https://files.phpmyadmin.net/phpMyAdmin/4.9.0.1/phpMyAdmin-4.9.0.1-all-languages.zip
安装zip，并解压下载的源代码
#unzip phpMyAdmin-4.9.0.1-all-languages.zip
移动phpMyAdmin源代码到网页目录下并授权www-data
# mv phpMyAdmin-4.9.0.1-all-languages /home/wwwroot/网站目录/phpMyAdmin && chown -R www-data:www-data /home/wwwroot/网站目录/phpMyAdmin
```

配置phpMyAdmin的短语密码

```
sed -i "108 s/''/'自己随机生成32为密码就行'/g" /usr/share/nginx/html/phpMyAdmin/libraries/config.default.php
#示例：
sed -i "108 s/''/'M4HUkD8rwVPo919e36Rp5zItiPqprByh'/g" /home/wwwroot/网站目录/phpMyAdmin/libraries/config.default.php
```

### 顺带说一下网站迁移

本来一直使用wordpress插件wvividbackup备份数据库+wordpress文件，但实际使用wvividbackup备份文件导入迁移网站有很多问题，现在我迁移方法是在原服务器zip打包网站目录，scp到新服务器；源数据库通过phpMyAdmin导出，在新服务器MySQL创建同名数据库和用户再用phpMyAdmin导入。这样迁移很顺利。由于服务器系统和配置不同，此法是不是通用不一定。
