##ZooKeeper安装

> Ubuntu下的安装

- 下载Zookeeper源码包

  下载地址:(http://zookeeper.apache.org/releases.html),这里下载的最新稳定版本zookeeper-3.4.7.tar.gz

- 安装Zookeeper

  将Zookeeper解压到一个目录下,这里选择/home/sahinn/soft/storm

~~~Bash
sahinn@sahinn:~/soft/storm$ tar -zxvf zookeeper-3.4.7.tar.gz
~~~  

- 配置环境变量

~~~BASH
sahinn@sahinn:~/soft/storm$ vim ~/.bashrc

#Zookeeper
export ZOOKEEPER_HOME=/home/sahinn/soft/storm/zookeeper-3.4.7
export ZOOKEEPER=$ZOOKEEPER_HOME/bin
export PATH=$ZOOKEEPER:$PATH

#另外JDK的CLASSPATH中加入ZOOKEEPER的lib
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:${ZOOKEEPER_HOME}/lib

sahinn@sahinn:~/soft/storm$ source ~/.bashrc
~~~

- 配置Zookeeper

~~~BASH
sahinn@sahinn:~$ cd /home/sahinn/soft/storm/zookeeper-3.4.7/conf/
#复制一份配置文件
sahinn@sahinn:~/soft/storm/zookeeper-3.4.7/conf$ cp zoo_sample.cfg zoo.cfg

#下面介绍如何修改配置文件

注意下面配置文件里面别包含汉字，以下只为解释用。

# The number of milliseconds of each tick
tickTime=2000 #客户端与服务器之间维持心跳的时间间隔
# The number of ticks that the initial
# synchronization phase can take
initLimit=10  #初始化的限制数
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5    #心跳传输ack的限制数
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/home/sahinn/soft/storm/zookeeper-3.4.7/data  # Zookeeper 保存数据的目录,日志等
# the port at which the clients will connect
clientPort=2181   #服务器监听的端口
~~~

- 启动Zookeeper

~~~BASH
#检查端口信息
sahinn@sahinn:~/soft/storm/zookeeper-3.4.7/conf$ sudo netstat -anputl | grep 2181

sahinn@sahinn:~/soft/storm/zookeeper-3.4.7/bin$ ./zkServer.sh start  #启动

sahinn@sahinn:~/soft/storm/zookeeper-3.4.7/bin$ ./zkServer.sh stop #停止
~~~

- 修改日志路径

由于默认的日志路径就在bin/下面，而且没有按天整理所以这里重新配置下。

~~~BASH
#修改bin/目录下的zkEnv.sh文件
#将ZOO_LOG_DIR配置为自己需要的日志路径
#ZOO_LOG4J_PROP按天生成

if [ "x${ZOO_LOG_DIR}" = "x" ]
then
    ZOO_LOG_DIR="/home/sahinn/soft/storm/zookeeper-3.4.7/data/logs"
fi

if [ "x${ZOO_LOG4J_PROP}" = "x" ]
then
    ZOO_LOG4J_PROP="INFO,ROLLINGFILE"
fi

#修改con/目录下log4j文件
#按天生成
zookeeper.root.logger=INFO, DaliyRollingFileAppender
~~~

到目前为止基本的安装就完成了。
