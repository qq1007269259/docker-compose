# docker-lnmp
通过docker-compose搭建本地lnmp环境

### NGINX
  nginx版本为：1.13.10
  
  
  编译安装时参数为：
  
  
    ./configure 
      --prefix=/usr/local/nginx 
      --with-http_ssl_module 
      --with-http_sub_module 
      --with-http_dav_module 
      --with-http_flv_module 
      --with-http_gzip_static_module 
      --with-http_stub_status_module 
      --with-debug

### PHP
  php版本为：7.1
  
  
  编译安装参数为：
  
  
    ./configure
      --prefix=/usr/local/php 
      --with-mysqli 
      --with-pdo-mysql 
      --with-iconv-dir 
      --with-freetype-dir 
      --with-jpeg-dir 
      --with-png-dir 
      --with-zlib 
      --with-libxml-dir 
      --enable-simplexml 
      --enable-xml 
      --disable-rpath 
      --enable-bcmath 
      --enable-soap 
      --enable-zip 
      --with-curl 
      --enable-fpm 
      --with-fpm-user=nobody 
      --with-fpm-group=nobody 
      --enable-mbstring 
      --enable-sockets 
      --with-mcrypt 
      --with-gd 
      --enable-gd-native-ttf 
      --with-openssl 
      --with-mhash 
      --enable-opcache
  另外安装添加了redis扩展
  
 ### MYSQL
  直接采用docker官方5.7版本的镜像库
  
### 使用方法
```
git clone git@github.com:xuweijie4030/docker-compose.git
```

##### 配置docker-compose.yml
```
version: '2'

services:
  nginx:
    image: xuweijie/nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /www/lnmp-etc/nginx/conf/nginx.conf:/usr/local/nginx/conf/nginx.conf   :前面为本地nginx配置文件路径，可自行修改为本机配置文件路径，以下所有volumes选项都是如此，所有原配置文件都在lnmp-etc中
      - /www/lnmp-etc/nginx/conf/vhost:/usr/local/nginx/conf/vhost
      - /www/lnmp-etc/nginx/logs/access.log:/usr/local/nginx/logs/access.log
      - /www/lnmp-etc/nginx/logs/error.log:/usr/local/nginx/logs/error.log
      - /www/html:/var/www/html
    links:
      - php:php
  php:
    image: xuweijie/php71
    expose:
      - "9000"
    volumes:
      - /www/lnmp-etc/php71/php.ini:/usr/local/php/lib/php.ini
      - /www/html:/var/www/html
    links:
      - mysql:mysql

  mysql:
    image: mysql:5.7
    ports:
      - "3306:3306"
    volumes:
      - /www/lnmp-etc/mysql/etc:/etc/mysql
      - /www/lnmp-etc/mysql/data:/var/lib/mysql   该项相对比较特殊，为mysql原有数据库文件
      - /www/lnmp-etc/mysql/logs/error.log:/var/log/mysql/error.log
    environment:
      MYSQL_ROOT_PASSWORD: "1"   这里用来设置mysql登录密码
```

在当前目录执行
```
docker-compose up -d
```
