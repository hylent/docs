# CentOS 7 服务器配置

## 更新系统

```
yum update
```

## 关闭可选功能和服务

selinux

```
vi /etc/selinux/config
SELINUX=disabled
```

邮件服务postfix

```
systemctl stop postfix
systemctl disable postfix
```

防火墙firewalld

```
systemctl stop firewalld
systemctl disable firewalld
```

## 安装软件依赖

```
yum install -y \
net-tools wget \
gcc gcc-c++ make automake autoconf libtool cmake \
zlib-devel libxml2-devel bzip2 bzip2-devel pcre-devel \
curl-devel openssl-devel \
libjpeg-devel libpng-devel freetype-devel libXpm-devel t1lib-devel \
gettext-devel readline-devel \

ln -sv /usr/lib64/libssl.so /usr/lib/
ln -sv /usr/lib64/libXpm.so /usr/lib/
```

## 创建用户

```
groupadd www
useradd www -g www -s /sbin/nologin

groupadd mysql
useradd mysql -g mysql -s /sbin/nologin
```

## 下载源代码

```
wget http://nginx.org/download/nginx-1.6.3.tar.gz
wget ftp://mcrypt.hellug.gr/pub/crypto/mcrypt/libmcrypt/libmcrypt-2.5.7.tar.gz
wget http://nchc.dl.sourceforge.net/project/mhash/mhash/0.9.9.9/mhash-0.9.9.9.tar.bz2
wget http://cn2.php.net/distributions/php-5.5.24.tar.bz2
wget https://github.com/phpredis/phpredis/archive/2.2.7.tar.gz -O phpredis-2.2.7.tar.gz
wget http://download.redis.io/releases/redis-3.0.0.tar.gz
wget http://cdn.mysql.com/Downloads/MySQL-5.6/mysql-5.6.24.tar.gz
wget http://nodejs.org/dist/v0.12.2/node-v0.12.2.tar.gz
```

## 配置ldconfig
echo '/usr/local/lib' > /etc/ld.so.conf.d/local.conf

## 安装依赖库
libmcrypt

```
cd /usr/local/src
tar xzf ~/libmcrypt<Tab>
cd libmcrypt<Tab>
./configure && make -j4 && make install
ldconfig -v | grep libmcrypt
```

mhash

```
cd /usr/local/src
tar xjf ~/mhash<Tab>
cd mhash<Tab>
./configure && make -j4 && make install
ldconfig -v | grep mhash
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

## 安装PHP

```
cd /usr/local/src
tar xzf ~/php<Tab>
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
--with-gettext \
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
mv /etc/my.cnf /etc/my.cnf.back

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
systemctl start mysql
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
