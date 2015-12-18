##安装Storm依赖安装

>各版本Linux安装可能都不太一样，这里我用的还是自己的Ubuntu15.04工作机来安装的。

>生产环境可能是CentOS或者其他版本Linux

>如果使用Netty作为消息中间件的话，可以不用安装zeromq，jzeromq


- 安装Python
- 安装JDK配置
- 安装unzip

以上安装参考:[Ubuntu工作环境搭建](https://github.com/ericzuobin/notes/blob/master/ubuntu/ubuntu_init.md)

- 安装Zookeeper

Zookeeper安装参考:[ZooKeeper安装与配置](https://github.com/ericzuobin/notes/blob/master/storm/zookeeper-install.md)

- 安装zeromq

~~~BASH
wget http://download.zeromq.org/zeromq-2.1.7.tar.gz

tar zxf zeromq-2.1.7.tar.gz

cd zeromq-2.1.7

./configure

make

sudo make install

sudo ldconfig
~~~

个人Ubuntu下配置编译环境时出错，记录如何解决:

~~~BASH
configure: error: cannot link with -luuid, install uuid-dev.

sahinn@sahinn:~/soft/storm/zeromq-2.1.7$ sudo apt-get install uuid-dev
~~~

如果没有root权限，或当前用户无sudo权限时，执行 “ ./configure --prefix=/home/xxxxx” 替换 “./configure”, 其中/home/xxxx 为安装目标目录

- 安装jzmq

~~~BASH
git clone git://github.com/nathanmarz/jzmq.git

cd jzmq

./autogen.sh

./configure

make
~~~

运行autogen,make时出错出错
~~~BASH
#以下是运行autogen出错
sahinn@sahinn:~/soft/storm/jzmq$ ./autogen.sh
autogen.sh: error: could not find libtool.  libtool is required to run autogen.sh.

sahinn@sahinn:~/soft/storm/jzmq$ sudo apt-get install libtool

#Ubuntu15.04上安装后默认是libtoolize，加个软连接
sahinn@sahinn:~/soft/storm/jzmq$ sudo ln -s /usr/bin/libtoolize /usr/bin/libtool

#接下来遇到
autogen.sh: error: could not find autoreconf.  autoconf and automake are required to run autogen.sh.

sahinn@sahinn:~/soft/storm/jzmq$ sudo apt-get install autoconf

#以下是运行make出错
#注意需要创建下面的文件
cd jzmq/src
touch classdist_noinst.stamp

##重新定义CLASSPATH
CLASSPATH=.:./.:$CLASSPATH javac -d . org/zeromq/ZMQ.java org/zeromq/ZMQException.java org/zeromq/ZMQQueue.java org/zeromq/ZMQForwarder.java org/zeromq/ZMQStreamer.java

#然后
make
sudo make install
~~~

make install
如果没有root权限，或当前用户无sudo权限时，执行 “ ./configure --prefix=/home/xxxx --with-zeromq=/home/xxxx” 替换 “./configure”, 其中/home/xxxx 为安装目标目录

安装完之后下面会生成两个jar包

sahinn@sahinn:/usr/local/share/java/zmq.jar

sahinn@sahinn:/home/sahinn/soft/storm/jzmq/perf/zmq-perf.jar
