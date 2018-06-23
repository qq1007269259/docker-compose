### 使用方法
##### 第一步 下载所有内容
```
git clone git@github.com:xuweijie4030/docker-compose.git
```

##### 第二步 配置docker-compose.yml
```
version: '2'

services:
  # 这里的名字可以自定义，服务启动后以这里的名字来命名容器的名字
  nginx:
    # 这里是我们要引用的容器镜像，本地如果没有该镜像会自动从线上库拉取
    image: registry.cn-hangzhou.aliyuncs.com/xwjs/nginx
    # 这里是我们要和宿主机进行映射的端口
    ports:
      - "80:80"
      - "443:443"
    # 这里用来配置我们需要挂载的目录或文件，主要有配置文件、日志文件和代码
    volumes:
      # ：前面是宿主机文件或目录的地址，：后面是容器中对应文件或目录所在地址，只需修改前面宿主机文件或目录地址即可
      - $PWD/nginx/nginx.conf:/usr/local/nginx/conf/nginx.conf
      - $PWD/nginx/vhost:/usr/local/nginx/conf/vhost
      - $PWD/nginx/logs/:/usr/local/nginx/logs/
    # 引用代码容器中的代码
    volumes_from:
      - code
    # 这里是该容器需要链接的其他容器
    links:
      - php:php
    # 依赖容器
    depends_on:
      - code
      - php
    # 指定启动后的容器的名字
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
