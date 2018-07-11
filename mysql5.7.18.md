一、mysql5.7的主要特性

        （1）原生支持Systemd
        （2）更好的性能：对于多核CPU、固态硬盘、锁有着更好的优化
        （3）更好的InnoDB存储引擎
        （4）更为健壮的复制功能：复制带来了数据完全不丢失的方案，传统金融客户也可以使用mysql数据库
        （5）新增sys库：以后这会是DBA访问最频繁的库
        （6）更好的优化器： 优化器代码重构的意义将在这个版本及以后版本中带来巨大的改进，Oracle官方正在解决mysql之前最大的难题原生JSON类型的支持（JavaScript object Notation）
         注：JSON（JavaScript Object Notation）是一种轻量级的数据交换格式。JSON采用完全独立于语言的文本格式，但是也使用了类似c语言家族的习惯（包括c、c++、c#、java、JavaScript、Perl、Python）等。这些特性使json称为理想的数据交换语言。易于阅读和编写，同时也易于机器解析和生成（对于提升网路传输速率有帮助）。
二、安装前准备

     1.由于centos7.2默认安装的数据库是mariadb-libs，这样安装时会你报错，首先查看一下mariadb-libs是否已经安装
        
        [xxxxx@xxxxxx ~]$ rpm  -qa | grep  mariadb-libs
        mariadb-libs-5.5.52-1.el7.x86_64
    
    //卸载mariadb-libs
        [xxxxx@xxxxxx ~]$ sudo rpm -e mariadb-libs --nodeps
    
    2.安装相关依赖包
        cmake：从mysql5.5版本开始弃用了configure编译器，改用CMake编译器，用于设置mysql的编译参数。例如：安装目录、数据存放目录、字符编码、排序规则等。 
        boost：从mysql5.7.5开始boost库是必需的，mysql源码中用到了c++的boost库，要求必须安装boost1.59.0或以上版本 GCC是Linux下的C语言编译工具，mysql源码编译完全由c和c++编写，要求必须安装GCC bison：Linux下C/C++语法分析器 ncurses：字符终端处理库 
        //下载cmake(cd /usr/local/src)
        sudo wget https://cmake.org/files/v3.3/cmake-3.3.2.tar.gz
        sudo tar zxvf cmake-3.3.2.tar.gz
        cd cmake-3.3.2/
        sudo ./bootstrap
        sudo gmake && gmake install(这个要跑一会儿)
        [xxxxx@xxxxxx ~]$ cmake -version
        cmake version 3.3.2
        //安装bison-3.0
        sudo wget ftp://ftp.gnu.org/gnu/bison/bison-3.0.tar.gz
        [xxxxx@xxxxxx src]$ sudo zxvf bison-3.0.tar.gz 
        [xxxxx@xxxxxx src]$ cd  bison-3.0
        [xxxxx@xxxxxx bison-3.0]$ ./configure && make && make install
        //下载ncurses5.9
        [xxxxx@xxxxxx src]$ sudo wget http://ftp.gnu.org/gnu/ncurses/ncurses-5.9.tar.gz
        [xxxxx@xxxxxx src]$ sudo tar zxvf ncurses-5.9.tar.gz
        [xxxxx@xxxxxx src]$ cd ncurses-5.9
        [xxxxx@xxxxxx ncurses-5.9]$ sudo ./configure && make && make install
        //相关的依赖安装好后，就正式步入正题
        //创建mysql用户和组
        [xxxxx@xxxxxx src]$ sudo groupadd  -r mysql && useradd -r -g mysql -s /bin/false -M mysql
        //创建MySQL安装目录及数据库存放目录
        [xxxxx@xxxxxx src]$ sudo mkdir /usr/local/mysql 
        [xxxxx@xxxxxx src]$ sudo mkdir /usr/local/mysql/data
        //下载mysql5.7.18源码分带boost版的和不带boost版的。为了方便就直接下载带boost版的
        [xxxxx@xxxxxx src]$ sudo wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-boost-5.7.18.tar.gz
        [xxxxx@xxxxxx src]$ sudo tar zxvf mysql-boost-5.7.18.tar.gz
        [xxxxx@xxxxxx src]$ cd mysql-boost-5.7.18
        //执行cmake命令进行编译前的配置：
        [xxxxx@xxxxxx mysql-boost-5.7.18]$ cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/usr/local/mysql/data -DSYSCONFDIR=/etc -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DEXTRA_CHARSETS=all -DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_SYSTEMD=1 -DWITH_BOOST=boost
        [xxxxx@xxxxxx mysql-boost-5.7.18]$ make && make install（编译安装MySQL相对比较慢，内存低于1G的安装不了）
        //配置参数详解
        -DCMAKE_INSTALL_PREFIX=/usr/local/mysql #mysql安装目录
        
        -DMYSQL_DATADIR=/usr/local/mysql/data #数据库文件存放目录
        
        -DSYSCONFDIR=/etc #mysql配置文件所在目录
        
        -DWITH_MYISAM_STORAGE_ENGINE=1 #添加MYISAM引擎支持
        
        -DWITH_INNOBASE_STORAGE_ENGINE=1 #添加InnoDB引擎支持
        
        -DWITH_ARCHIVE_STORAGE_ENGINE=1 #添加ARCHIVE引擎支持
        
        -DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock #指定mysql.sock位置
        
        -DWITH_PARTITION_STORAGE_ENGINE=1 #安装支持数据库分区
        
        -DEXTRA_CHARSETS=all #使mysql支持所有的扩展字符
        
        -DDEFAULT_CHARSET=utf8 #设置mysql的默认字符集为utf8
        
        -DDEFAULT_COLLATION=utf8_general_ci #设置默认字符集校对规则
        
        -DWITH-SYSTEMD=1 #可以使用systemd控制mysql服务
        
        -DWITH_BOOST=/usr/local/boost #指向boost库所在目录（我下的MySQL5.7.18是集成boost的，所以可以直接写成-DWITH_BOOST=boost）
        //设置环境变量
        [xxxxx@xxxxxx mysql-boost-5.7.18]$ echo "export PATH=$PATH:/usr/local/mysql/bin" >> /etc/profile
        [xxxxx@xxxxxx mysql-boost-5.7.18]$ source /etc/profile
        //给mysql数据目录文件设置相关权限
        [xxxxx@xxxxxx mysql-boost-5.7.18]$ chown  -R  mysql:mysql  /usr/local/mysql/
        [xxxxx@xxxxxx mysql-boost-5.7.18]$ chmod 755 /usr/local/mysql/data/
        //初始化mysql数据库
        cd /usr/local/mysql
        mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
        //下面会返回初始化后的结果，最后一行后生成一个初始密码
        //随后要创建并修改MySQL主配置文件
        vi /etc/my.cnf
        [client]
        socket=/usr/local/mysql/mysql.sock
        [mysqld]
        basedir=/usr/local/mysql
        datadir=/usr/local/mysql/data
        pid-file=/usr/local/mysql/data/mysqld.pid
        socket=/usr/local/mysql/mysql.sock
        log-error=/usr/local/mysql/data/mysqld.err
        //注：重新扫描systemd，扫描新的或有变动的单元
        systemctl daemon-reload
        //配置MySQL自动启动
        cp /usr/local/mysql/usr/lib/systemd/system/mysqld.service  /usr/lib/systemd/system
        //接着启动MySQL
        systemctl start mysqld 
        //可能会报错误
        Job for mysqld.service failed because the control process exited with error code. See "systemctl status mysqld.service" and "journalctl -xe" for details.
        //既然报错，最好的思路就是查看MySQL日志
        tail /usr/local/mysql/data/mysqld.err
        //发现有一行很明显的error错误
        2018-07-05T08:41:46.246474Z 0 [ERROR] /usr/local/mysql/bin/mysqld: Can't create/write to file '/var/run/mysqld/mysqld.pid' (Errcode: 2 - No such file or directory)
        2018-07-05T08:41:46.246507Z 0 [ERROR] Can't start server: can't create PID file: No such file or directory
        //通过分析上面的错误日志发现，在mysqld.service，把默认的pid文件指定到了/var/run/mysqld/目录，并没有事先建立该目录，因此要手动建立该目录并把权限赋给mysql用户，这样绝对不会报错了
        mkdir /var/run/mysqld
        chown  -R  mysql:mysql /var/run/mysqld/
        //再次重新扫描systemd，扫描新的或有变动的单元
        systemctl daemon-reload
        //重新启动mysql服务
        systemctl start mysqld
        systemctl status mysqld.service
        //当你看到 Active: active (running) since Thu 2018-07-05 16:44:47 CST; 16s ago说明MySQL服务已成功启动啦！
        //接着就用到前面复制的临时密码了(#获取临时密码可以进到data目录，通过这种方式查看 cat mysqld.err | grep password)

        mysql -uroot -p '你复制的密码'
        //登录上mysql后可以改密码
        （1）SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');
        （2）直接修改user表
          mysql> use mysql;
        　mysql> UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';
        　mysql> FLUSH PRIVILEGES;
        （3）直接修改MySQL的配置文件
         在[mysqld]的段中加上一句：skip-grant-tables 等等（还有其他的方法都差不多）
         //为了方便管理mysql，直接通过navicat链接远程数据库，你只需两条命令即可搞定
         GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '你的mysql密码' WITH GRANT OPTION;
         flush privileges;
    
        
        