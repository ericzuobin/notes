##Storm安装

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

- 安装Storm

~~~BASH
wget http://apache.fayea.com/storm/apache-storm-0.9.5/apache-storm-0.9.5.zip

unzip apache-storm-0.9.5.zip

sahinn@sahinn:~/soft/storm$ sudo ln -s apache-storm-0.9.5/ /usr/local/storm

sahinn@sahinn:~/soft/storm$ vim ~/.bashrc
#添加以下环境变量
#Storm
export STORM_HOME=/home/sahinn/soft/storm/apache-storm-0.9.5
export PATH=$PATH:$STORM_HOME/bin

sahinn@sahinn:~/soft/storm$ source ~/.bashrc
~~~

至此，Storm安装完毕。

##Storm配置

#### 单机版配置

- 启动zookeeper：

/usr/local/zookeeper/bin/zkServer.sh 单机版直接启动，不用修改什么配置。

- 配置storm：

文件在/usr/local/storm/conf/storm.yaml

~~~BASH
storm.zookeeper.servers:
- 127.0.0.1

storm.zookeeper.port: 2181

nimbus.host: "127.0.0.1"

storm.local.dir: "/tmp/storm"

supervisor.slots.ports:
- 6700
- 6701
- 6702
- 6703
~~~

1. storm.zookeeper.servers: Storm集群使用的Zookeeper集群地址
2. storm.zookeeper.port: zookeeper端口
3. storm.local.dir: Nimbus和Supervisor进程用于存储少量状态，如jars、confs等的本地磁盘目录，需要提前创建该目录并给以足够的访问权限。
4. java.library.path:Storm使用的本地库（ZMQ和JZMQ）加载路径。默认为”/usr/local/lib:/opt/local/lib:/usr/lib”，一般来说ZMQ和JZMQ默认安装在/usr/local/lib 下，因此不需要配置即可。
5. nimbus.host: Storm集群Nimbus机器地址，各个Supervisor工作节点需要知道哪个机器是Nimbus，以便下载Topologies的jars、confs等文件。
6. supervisor.slots.ports: 对于每个Supervisor工作节点，需要配置该工作节点可以运行的worker数量。每个worker占用一个单独的端口用于接收消息，该配置选项即用于定义哪些端口是可被worker使用的。默认情况下，每个节点上可运行4个workers，分别在6700、6701、6702和6703端口。


以下是启动Storm各个后台进程的方式：

Nimbus: 在Storm主控节点上运行”bin/storm nimbus >/dev/null 2>&1 &”启动Nimbus后台程序，并放到后台执行；

Supervisor: 在Storm各个工作节点上运行”bin/storm supervisor >/dev/null 2>&1 &”启动Supervisor后台程序，并放到后台执行；

UI: 在Storm主控节点上运行”bin/storm ui >/dev/null 2>&1 &”启动UI后台程序，并放到后台执行，启动后可以通过http://{nimbus host}:8080观察集群的worker资源使用情况、Topologies的运行状态等信息。

运行jps命令看下进程情况

~~~BASH
sahinn@sahinn:/usr/local/storm$ jps
23659 core
23138 nimbus
12479 activemq.jar
23982 Jps
23555 supervisor
23144 QuorumPeerMain
~~~

运行ip:8080可以查看到Storm UI相关
