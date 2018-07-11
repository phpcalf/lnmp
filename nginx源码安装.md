Nginx1.4源码包安装

    安装前先用yum安装相关依赖以防止安装期间出现错误
    
    yum -y install gcc gcc-c++ autoconf automake
    yum -y install zlib zlib-devel openssl openssl-devel pcre-devel
    在这期间可能会报 yum Multilib Version Problems Found等错误(不要紧，此坑已帮你填满了)
    原因是因为多个库不能共存，不过更新的话也并不行，但是可以在安装命令后面加上
    只需要在后面加上下面的命令

    --setopt=protected_multilib=false
    完整命令就是

    sudo yum -y install zlib zlib-devel openssl openssl-devel pcre-devel --setopt=protected_multilib=false
    ok,完美解决,Perfect!

    接下来就是重点了，首先下载源码包，

    nginx 下载地址 http://nginx.org/en/download.html

    //下载的方式有很多，本地下载好后通过ftp/rz上传，更快捷的直接通过wget 
    //我一般习惯将下载包统一放置于(/usr/local/src )下便于管理
    cd /usr/local/src 
    sudo wget http://nginx.org/download/nginx-1.14.0.tar.gz
    //解压
    tar -zxvf nginx-1.14.0.tar.gz
    cd nginx-1.14.0
    //编译安装 (/usr/local/nginx安装目录、with-http_stub_status_module模块主要用于查看Nginx的一些状态信息，with-http_ssl_module模块配置支持HTTPS访问)
    ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
    sudo make && make install
    //linux下的软连接就是为了方便广大程序猿少敲代码的
    sudo ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx