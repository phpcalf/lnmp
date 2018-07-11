1、首先来下载并安装相关依赖

    yum -y install make gcc gcc-c++ gcc-g77 flex file libtool libtool-libs kernel-devel libjpeg libjpeg-devel libpng libpng-devel libpng10 libpng10-devel\
    gd gd-devel freetype-devel libxml* libxml2-devel zlib zlib-devel \
    glib2 glib2-devel bzip2 bzip2-devel libevent* libevent-devel 
    curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel \
    gettext gettext-devel gmp-devel pspell-devel unzip libcap lsof
      
    jpegsrc（gd库需要）
    [xxxxx@xxxxxx src]# wget http://www.ijg.org/files/jpegsrc.v9a.tar.gz
    tar zxvf jpegsrc.v9a.tar.gz
    cd jpegsrc.v9a
    [xxxxx@xxxxxx jpegsrc.v9a]# ./configure --prefix=/usr/local/jpeg && make && make install
    freetype（gd库需要） 
    [xxxxx@xxxxxx src]# wget http://download.savannah.gnu.org/releases/freetype/ft2demos-2.5.3.tar.gz
    tar zxvf ft2demos-2.5.3.tar.gz
    cd ft2demos-2.5.3
    [xxxxx@xxxxxx ft2demos-2.5.3]# ./configure --prefix=/usr/local/freetype && make && make install
    libpng（gd库需要）
    [xxxxx@xxxxxx src]# wget ftp://ftp.simplesystems.org/pub/png/src/libpng16/libpng-1.6.28.tar.gz
    tar zxvf libpng-1.6.28.tar.gz
    cd libpng-1.6.28
    [xxxxx@xxxxxx libpng-1.6.28]# ./configure --prefix=/usr/local/libpng && make && make install
    gd库可以先不安装，之后通过phpize来进行扩展安装
    正式安装php
    [xxxxx@xxxxxx src]# cd php-7.2.5/
    [xxxxx@xxxxxx php-7.2.5]# ./configure --prefix=/usr/local/php --with-config-file-path=/etc --with-mysql-sock=/tmp/mysql.sock  --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --enable-fpm --with-fpm-user=nginx  --with-fpm-group=nginx --enable-inline-optimization --disable-debug --disable-rpath --enable-shared  --enable-soap  --with-libdir=lib64  --with-xmlrpc --with-openssl --enable-sockets --with-mhash --with-pcre-regex --with-sqlite3 --with-zlib --with-iconv --with-bz2 --with-curl --enable-fileinfo --with-jpeg-dir=/usr/local/jpeg --with-png-dir=/usr/local/libpng  --with-freetype-dir=/usr/local/freetype  --enable-json --enable-mbstring --enable-pdo
    [xxxxx@xxxxxx php-7.2.5]# make && make install
    //这个编译需要二十分钟左右，耐心等待！