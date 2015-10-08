# Ubuntu Server 14.04 配置

## 网络配置

配置静态IP

```
vi /etc/network/interfaces

iface eth0 inet static
address 192.168.1.22
netmask 255.255.255.0
gateway 192.168.1.1
```

配置DNS

```
vi /etc/resolvconf/resolv.conf.d/tail

nameserver 223.5.5.5
nameserver 223.6.6.6
```

## 配置SSH root 远程登录

```
vi /etc/ssh/sshd_config

PermitRootLogin yes
```

## 更新系统

```
apt-get upgrade
```

## 安装软件依赖

```
apt-get install build-essential automake re2c libtool cmake

apt-get install \
libbz2-dev \
libxml2-dev \
libpcre3-dev \
libmcrypt-dev \
libmhash-dev \
libssl-dev \
libcurl4-openssl-dev \
libjpeg-dev \
libpng12-dev \
libfontconfig1-dev \
libxpm-dev \
libt1-dev \
libreadline-dev \
libncurses5-dev \
libbison-dev
```

## 下载源码

```
wget http://nginx.org/download/nginx-1.6.3.tar.gz
wget http://cn2.php.net/distributions/php-5.5.24.tar.bz2
wget http://cdn.mysql.com/Downloads/MySQL-5.6/mysql-5.6.24.tar.gz
wget http://download.redis.io/releases/redis-3.0.0.tar.gz
wget https://github.com/phpredis/phpredis/archive/2.2.7.tar.gz -O phpredis-2.2.7.tar.gz
wget http://nodejs.org/dist/v0.12.2/node-v0.12.2.tar.gz
```

## 创建用户

```
groupadd www
useradd www -g www -s /usr/sbin/nologin -d /home/www
mkdir /home/www
chown www.www /home/www

groupadd mysql
useradd mysql -g mysql -s /usr/sbin/nologin -d /home/mysql
mkdir /home/mysql
chown mysql.mysql /home/mysql
```

## 安装nginx

```
cd /usr/local/src
tar xzf ~/nginx<Tab>
cd nginx<Tab>
./configure && make -j4 && make install
ln -sv /usr/local/nginx/sbin/* /usr/sbin/
```

## 配置nginx

```
cd /usr/local/nginx/conf
mkdir hosts
vi nginx.conf

user                www;
worker_processes    4;
error_log           logs/error.log;
pid                 logs/nginx.pid;
events {
    worker_connections  1024;
}
http {
    include             mime.types;
    default_type        application/octet-stream;
    sendfile            on;
    keepalive_timeout   0;
    charset             utf-8;
    server {
        listen      80;
        server_name localhost;
        root        /home/www/default;
        location / {
            index   index.html index.php;
        }
        location ~ \.php$ {
            fastcgi_pass    127.0.0.1:9000;
            fastcgi_index   index.php;
            fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include         fastcgi_params;
        }
    }
    include hosts/*.conf;
}
```

## 启动nginx

```
nginx
```

## 开机启动nginx

```
cd /etc/init.d
wget https://raw.githubusercontent.com/JasonGiedymin/nginx-init-ubuntu/master/nginx
chmod +x nginx
cd ../etc/rc2.d
ln -sv ../init.d/nginx S80nginx
```

## 安装PHP

```
cd /usr/local/src
tar xjf ~/php<Tab>
cd php<Tab>

./configure \
--prefix=/usr/local/php \
--with-config-file-path=/usr/local/php/etc \
--with-config-file-scan-dir=/usr/local/php/ini.d \
--enable-fpm \
--with-fpm-user=www \
--with-fpm-group=www \
--enable-bcmath \
--enable-calendar \
--enable-exif \
--enable-ftp \
--enable-mbstring \
--enable-sockets \
--enable-zip \
--with-zlib \
--with-curl=/usr \
--with-bz2=/usr \
--with-openssl=/usr \
--with-mcrypt \
--with-mhash \
--with-readline \
--with-gd \
--with-jpeg-dir=/usr \
--with-png-dir=/usr \
--with-xpm-dir=/usr \
--with-freetype-dir=/usr \
--with-t1lib=/usr \
--with-mysql=mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--without-pear \
--with-libdir=/usr/lib/x86_64-linux-gnu \
&& make -j4 && make install

ln -sv /usr/local/php/bin/* /usr/bin/
ln -sv /usr/local/php/sbin/* /usr/sbin/
```

## 配置PHP

```
mkdir /usr/local/php/ini.d
cd /usr/local/php/etc

cp /usr/local/src/php<Tab>/php.ini<Tab> ./php.ini
vi php.ini
/date
/charset
/2M
/8M

cp php-fpm.conf.default php-fpm.conf
vi php-fpm.conf
/pid
/max_children
```

## 安装PHP扩展

phpredis

```
cd /usr/local/src
tar ~/phpredis<Tab>
cd phpredis<Tab>
phpize
./configure && make -j4 && make install

echo 'extension=redis.so' > /usr/local/php/ini.d/redis.ini
```

nc

```
cp nc.so `php-config --extension-dir`
echo 'extension=nc.so' > /usr/local/php/ini.d/nc.ini
```

## 启动php-fpm

```
php-fpm
```

## 安装Redis

```
cd /usr/local/src
tar ~/redis<Tab>
cd redis<Tab>
make -j4 && make install
cp redis.conf /etc/
```

## 配置Redis

```
vi /etc/redis.conf

daemonize yes
dir /tmp/
bind 127.0.0.1
```

## 启动Redis

```
redis-server /etc/redis.conf
```

## 安装MySQL

```
cd /usr/local/src
tar xzf ~/mysql<Tab>
cd mysql<Tab>
cmake -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci
make -j4 && make install
ln -sv /usr/local/mysql/bin/* /usr/bin/

cd /usr/local/mysql
scripts/mysql_install_db --basedir=/usr/local/mysql --datadir=/home/mysql/data --user=mysql
cp support-files/mysql.server /etc/init.d/mysql
```

## 配置MySQL

```
vi /usr/local/mysql/my.cnf

basedir = /usr/local/mysql
datadir = /home/mysql/data
port = 3306
server_id = 1
socket = /tmp/mysql.sock
bind_address = 127.0.0.1
```

## 启动MySQL

```
service mysql start
```

## 管理MySQL用户

```
mysql -p

use mysql;
delete from user where user = '';
update user set password = password('root') where user = 'root';

create database nc default charset utf8;
grant all on nc.* to nc identified by 'nc';
flush privileges;

quit
```
