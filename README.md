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
  # 这里的名字可以自定义，服务启动后以这里的名字来命名容器的名字
  nginx:
    # 这里是我们要引用的容器镜像，本地如果没有该镜像会自动从线上库拉取
    image: xuweijie/nginx
    # 这里是我们要和宿主机进行映射的端口
    ports:
      - "80:80"
      - "443:443"
    # 这里用来配置我们需要挂载的目录或文件，主要有配置文件、日志文件和代码
    volumes:
      # ：前面是宿主机文件或目录的地址，：后面是容器中对应文件或目录所在地址，只需修改前面宿主机文件或目录地址即可
      - /yourpath/etc/nginx/conf/nginx.conf:/usr/local/nginx/conf/nginx.conf
      - /yourpath/etc/nginx/conf/vhost:/usr/local/nginx/conf/vhost
      - /yourpath/etc/nginx/logs/access.log:/usr/local/nginx/logs/access.log
      - /yourpath/etc/nginx/logs/error.log:/usr/local/nginx/logs/error.log
      - /your/web/path:/var/www/html
    # 这里是该容器需要链接的其他容器
    links:
      - php:php
    # 指定启动后的容器的名字
    container_name: nginx
  php:
    image: xuweijie/php71
    expose:
      - "9000"
    volumes:
      - /yourpath/etc/php71/php.ini:/usr/local/php/lib/php.ini
      - /yourpath/etc/php71/php-fpm.d/www.conf:/usr/local/php/etc/php-fpm.d/www.conf
      - /your/web/path:/var/www/html
    links:
      - mysql:mysql
    container_name: php

  mysql:
    image: mysql:5.7
    ports:
      - "3306:3306"
    # 对于mysql而言除了需要挂载配置文件和日志文件外还需要将数据库文件进行挂载
    volumes:
      - /yourpath/etc/mysql/etc:/etc/mysql
      - /yourpath/etc/mysql/data:/var/lib/mysql
      - /yourpath/etc/mysql/logs/error.log:/var/log/mysql/error.log
    # 这里用来配置该容器的环境变量，这里主要用来设置mysql的root密码，可自行修改
    environment:
      MYSQL_ROOT_PASSWORD: "1"
    container_name: mysql

```

在当前目录执行
```
docker-compose up -d
```
