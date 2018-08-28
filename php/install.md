## 安装php

### 1.安装依赖

```
#: yum install -y \
  gcc-c++ autoconf \
  libjpeg libjpeg-devel libpng \
  libpng-devel freetype freetype-devel \
  libpng libpng-devel libxml2 libxml2-devel \
  zlib zlib-devel glibc glibc-devel \
  glib2 glib2-devel bzip2 bzip2-devel \
  ncurses curl openssl-devel \
  gdbm-devel db4-devel libXpm-devel \
  libX11-devel gd-devel gmp-devel \
  readline-devel libxslt-devel \
  expat-devel xmlrpc-c xmlrpc-c-devel \
  libicu-devel libmcrypt-devel \
  libmemcached-devel
```

### 2.下载php安装包并编译安装

```
#: cd /usr/src/
#: sudo wget http://php.net/get/php-7.1.21.tar.bz2/from/a/mirror(下载安装包)
#: sudo tar -jxvf php-7.1.21.tar.bz2 
#: cd php-7.1.21
#: sudo ./configure --prefix=/usr/local/php7.1 \
   --with-mysql-sock --with-mysqli \
   --enable-fpm  --enable-soap \
   --with-libxml-dir --with-openssl \
   --with-mcrypt --with-mhash \
   --with-pdo-pgsql \
   --with-pcre-regex  --with-zlib \
   --enable-bcmath --with-iconv \
   --with-bz2 --enable-calendar \
   --with-curl --with-cdb --enable-dom \
   --enable-exif --enable-fileinfo \
   --enable-filter --with-pcre-dir \
   --enable-ftp --with-gd \
   --with-openssl-dir --with-jpeg-dir \
   --with-png-dir --with-zlib-dir \
   --with-freetype-dir \
   --enable-gd-native-ttf \
   --enable-gd-jis-conv --with-gettext \
   --with-gmp --with-mhash \
   --enable-json --enable-mbstring \
   --enable-mbregex \
   --enable-mbregex-backtrack \
   --with-libmbfl --with-onig \
   --enable-pdo --with-pdo-mysql \
   --with-zlib-dir  --with-readline \
   --enable-session --enable-shmop \
   --enable-simplexml --enable-sockets \
   --enable-sysvmsg --enable-sysvsem \
   --enable-sysvshm --enable-wddx \
   --with-libxml-dir  --with-xsl \
   --enable-zip \
   --enable-mysqlnd-compression-support \
   --with-pear --enable-intl
   
#: sudo make
#: sudo make install

#: ln -sf /usr/local/php7/bin/php /usr/local/bin/php
#: php -v

添加php.ini,如果是生产环境则cp对应的php.ini-production
#: cp php.ini-development /usr/local/php7/lib/php.ini

```
### 借鉴资料 [https://www.cnblogs.com/lglblogadd/p/8269668.html](https://www.cnblogs.com/lglblogadd/p/8269668.html)

### 3.[安装composer](https://docs.phpcomposer.com/00-intro.html#Downloading-the-Composer-Executable)(全局安装)

```$ sudo curl -sS https://getcomposer.org/installer | php
   $ sudo mv composer.phar /usr/local/bin/composer
```

## 报错
```
编译报错：configure: error: Cannot find libpq-fe.h. Please specify correct PostgreSQL installation path

解决办法：sudo yum -y install postgresql-devel
```