## 使用方法
##### 第一步 下载所有内容
```
git clone git@github.com:xuweijie4030/docker-compose.git
```

##### 第二步 配置docker-compose.yml
```
version: '2'

services:

  nginx:
    image: registry.cn-hangzhou.aliyuncs.com/xwjs/nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - $PWD/nginx/nginx.conf:/usr/local/nginx/conf/nginx.conf
      - $PWD/nginx/vhost:/usr/local/nginx/conf/vhost
      - $PWD/nginx/logs/:/usr/local/nginx/logs/
    volumes_from:
      - code
    links:
      - php:php
    depends_on:
      - code
      - php
    container_name: nginx
    
  php:
    image: registry.cn-hangzhou.aliyuncs.com/xwjs/php71
    expose:
      - "9000"
    volumes:
      - $PWD/php/php.ini:/usr/local/php/lib/php.ini
      - $PWD/php/www.conf:/usr/local/php/etc/php-fpm.d/www.conf
    volumes_from:
      - code
    links:
      - mysql:mysql
      - redis:redis
    container_name: php

  redis:
    image: registry.cn-hangzhou.aliyuncs.com/xwjs/redis
    ports:
      - "6379:6379"
    volumes:
      - $PWD/redis/redis.conf:/usr/local/redis/redis.conf
    container_name: redis

  mysql:
    image: registry.cn-hangzhou.aliyuncs.com/xwjs/mysql
    ports:
      - "3306:3306"
    volumes:
      - $PWD/mysql/data:/var/lib/mysql/
    container_name: mysql
    
  code:
    image: registry.cn-hangzhou.aliyuncs.com/xwjs/code
    volumes:
      - /your_code_path:/usr/local/nginx/html
    container_name: code
    
##########################################################################################################################################

1. 目前Mac和Linux系统可正常使用，windows尚未测试
2. 开发环境中只需将代码挂载至code容器中即可进行第三步
3. 生产环境中请自行构建code镜像，并将代码直接复制至code镜像中

##########################################################################################################################################

```
##### 第三步 在当前目录执行
```
docker-compose up -d
```

## 内部镜像说明
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
  安装扩展：`redis` `mongodb` `swoole`
  
 ### MYSQL
  直接采用docker官方5.7版本的镜像库，默认用户名`root`，默认密码`1`
