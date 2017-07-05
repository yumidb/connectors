编译：
参考：https://yq.aliyun.com/articles/63187
没有列出的第三方库自行安装，如pg9.6.3，注意安装完pg后将
pgsql/bin加入path中，否则会影响编译

命令中省略sudo，如有需要自行添加

yum install git
yum install openssl-devel
yum install readline-devel

git clone https://github.com/EnterpriseDB/mongo_fdw.git

sh autogen.sh --with-master
运行到后面肯定会报错，实际上我们只希望通过这个脚本来初始化
一些第三方库以及编译选项。这个脚本很简单，看一遍就知道逻辑
顺序是如何的，可以根据自己的需要进行修改。

切换mongo-c-driver/src/libbson
./configure
make
make install

切换到mongo-c-driver
./configure --with-libbson=system --enable-ssl
make
make install

回到根目录，执行
export PKG_CONFIG_PATH=mongo-c-driver/src/:mongo-c-driver/src/libbson/src:$PKG_CONFIG_PATH

然后
make

如果生成mongo_fdw.so
ldd mongo_fdw.so
能看到  libmongoc-1.0.so.0 => /usr/local/pgsql/lib/libmongoc-1.0.so.0 (0x00007fdc2a529000)
        libbson-1.0.so.0 => /usr/local/pgsql/lib/libbson-1.0.so.0 (0x00007fdc2a2f7000)
就成功了


安装：
对应http://doc.sequoiadb.com/cn/SequoiaDB-cat_id-1432190714-edition_id-0，中将sdb统统换成mongo即可，唯一的区别是
我们安装较高版本的pg时多了一步user mapping的步骤
