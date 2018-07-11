1、首先来下载并安装相关依赖

    jpegsrc（gd库需要）
    wget http://www.ijg.org/files/jpegsrc.v9a.tar.gz
    freetype（gd库需要） 
    wget http://download.savannah.gnu.org/releases/freetype/ft2demos-2.5.3.tar.gz
    libpng（gd库需要）
    wget ftp://ftp.simplesystems.org/pub/png/src/libpng16/libpng-1.6.28.tar.gz
    gd库可以先不安装，之后通过phpize来进行扩展安装
    正式安装php
    ./configure --prefix=/usr/local/php --with-config-file-path=/etc --with-mysql-sock=/tmp/mysql.sock  --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --enable-fpm --with-fpm-user=nginx  --with-fpm-group=nginx --enable-inline-optimization --disable-debug --disable-rpath --enable-shared  --enable-soap  --with-libdir=lib64  --with-xmlrpc --with-openssl --enable-sockets --with-mhash --with-pcre-regex --with-sqlite3 --with-zlib --with-iconv --with-bz2 --with-curl --enable-fileinfo --with-jpeg-dir=/usr/local/jpeg --with-png-dir=/usr/local/libpng  --with-freetype-dir=/usr/local/freetype  --enable-json --enable-mbstring --enable-pdo
    //这个编译需要二十分钟左右，耐心等待！