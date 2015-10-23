>为了降低工作机mac的压力，把部分服务前移到ubuntu上面，记录全部操作。<br>
>注：如果想作为纯工作机，ubuntu可以选择不启动UI界面。<br>
>1.按ALT+CTRL+F1切换到字符界面（Linux实体机）<br>
>如果是VMware虚拟机安装的Linux系统，则切换到字符界面的时候需要以下操作<br>
>按下ALT+CTRL+SPACE(空格)，ALT+CTRL不松开，再按F1。这样就可以切换到字符界面了。<br><br>
2.按ALT+CTRL+F7切换到图形界面（Linux实体机）<br>
如果是VMware虚拟机安装的Linux系统，则切换到图形界面的时候需要以下操作<br>
按下ALT+CTRL+SPACE(空格)，ALT+CTRL不松开，<br>
再按F7。这样就可以切换到图形界面了。<br><br>
>如果想 Ubuntu 在每次启动到 command prompt ，可以输入以下指令:<br>
echo “false” \| sudo tee /etc/X11/default-display-manager<br>
当下次开机时，就会以命令行模式启动（text模式，字符界面登录），<br><br>
如果想变回图形界面启动（X windows启动），可以輸入:<br>
echo “/usr/sbin/gdm” \| sudo tee /etc/X11/default-display-manager<br><br>
如果在Ubuntn以命令行模式启动，<br>
在字符终端想回到图形界面的话只需以下命令:<br>
startx



####修改屏幕默认分辨率

>sudo gedit /etc/default/grub<br>
找到：#GRUB_GFXMODE=640x480 在这行下面加一行<br>
GRUB_GFXMODE=1920x1080<br>
保存后 sudo update-grub<br>
重启

####安装vim

>sudo apt-get install vim

####安装终端命令行工具 guake

>sudo apt-get install guake

>Guake随系统自动启动<br>
>1. 定位到[系统][首选项][启动程序]打开'启动程序喜好'程序。<br>
2. 单击'添加'按钮，在弹出的窗口中填写程序名称、命令和注释，<br>
命令一定不要写错。<br>
（可以是程序名字guake，也可以是命令的绝对路径如/usr/bin/guake，建议采用前者）<br>
>3. 修改以后不重叠的字体有:新宋体 bold,宋体,幼圆,consolas

####目录改成中文
>打开终端，在终端中输入命令:<br> 
export LANG=en_US<br>
xdg-user-dirs-gtk-update<br>

>在弹出的窗口中询问是否将目录转化为英文路径,同意并关闭.<br>

>在终端中输入命令:<br>
export LANG=zh_CN<br>

>关闭终端,并注销或重启.下次进入系统,系统会提示是否把转化好的目录改回中文.<br>
选择不许要并且勾上不再提示,并取消修改.主目录的中文转英文就完成了~<br>
>OK，现在你可以像linux迷一样痛快的使用cd命令了，cd /home/linuxmi/Downloads……<br>

####开启本机SSH
>本机开放SSH服务就需要安装openssh-server<br>
sudo apt-get install openssh-server<br>
>然后确认sshserver是否启动了：<br>
ps -e |grep ssh<br>
如果看到sshd那说明ssh-server已经启动了。<br>
如果没有则可以这样启动：sudo /etc/init.d/ssh start<br>
>ssh-server配置文件位于/ etc/ssh/sshd_config，在这里可以定义SSH的服务端口，默认端口是22，你可以自己定义成其他端口号，如222。<br>
>然后重启SSH服务：<br>
sudo /etc/init.d/ssh stop<br>
sudo /etc/init.d/ssh start<br>

>设置开机自启动：<br>
打开/etc/rc.local文件，在exit 0语句前加入：<br>
/etc/init.d/ssh start<br>

####安装telnet
>让ubuntu开启telnet服务做服务器<br>

>安装openbsd-inetd:<br>
sudo apt-get install openbsd-inetd<br>
>安装telnetd:<br>
sudo apt-get install telnetd<br>
>在etc/inetd.conf文件中可以看到这一行内容：<br>
telnet stream tcp nowait root /usr/sbin/tcpd /usr/sbin/in.telnetd<br>
如果没有这一行内容，就手动加上。<br>
重启openbsd-inetd<br>
/etc/init.d/openbsd-inetd restart<br>
查看telnet运行状态<br>
netstat -a | grep telnet<br>
>设置自启动<br>
打开/etc/rc.local文件，在exit 0语句前加入：<br>
/etc/init.d/openbsd-inetd start<br>

####安装和配置memcache
>安装Memcache服务端<br>
sudo apt-get install memcached<br>
安装完Memcache服务端以后，我们需要启动该服务：<br>
配置：memcached -d start -m 128 -l 172.16.22.250 -p 11211<br><br>
设置开机自启动<br>
打开/etc/rc.local文件，在exit 0语句前加入：<br>
/etc/memcached.conf  注释掉127.0.0.1<br>
/usr/bin/memcached -d start -m 128 -l 172.16.22.250 -p 11211<br><br>

####安装和配置JDK
>wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" 
http://download.oracle.com/otn-pub/java/jdk/7u79-b15/jdk-7u79-linux-i586.tar.gz<br><br>
>sudo mdkir /usr/java<br>
sudo tar -zxvf jdk-****-.tar.gz<br>

>1.配置环境变量：
sudo vim ~/.bashrc<br>
export JAVA_HOME=/usr/java/jdk1.7.0_79<br>
export JRE_HOME=${JAVA\_HOME}/jre<br>
export CLASSPATH=.:${JAVA\_HOME}/lib:${JRE\_HOME}/lib<br>
export PATH=${JAVA\_HOME}/bin:$PATH<br><br>

>2.配置默认JDK：<br>
sudo update-alternatives --install /usr/bin/java java /usr/java/jdk1.7.0_79/bin/java 300  <br>
sudo update-alternatives --install /usr/bin/javac javac /usr/java/jdk1.7.0_79/bin/javac 300  <br>
sudo update-alternatives --install /usr/bin/jar jar /usr/java/jdk1.7.0_79/bin/jar 300   <br>
sudo update-alternatives --install /usr/bin/javah javah /usr/java/jdk1.7.0_79/bin/javah 300  <br> 
sudo update-alternatives --install /usr/bin/javap javap /usr/java/jdk1.7.0_79/bin/javap 300<br>
sudo update-alternatives --config java<br>

####安装配置Maven
>sudo mkdir /opt/maven<br>
sudo mv /tmp/apache-maven-3.3.3-bin.tar.gz /opt/maven/<br>
sudo tar zxvf apache-maven-3.3.3-bin.tar.gz<br>

>配置环境变量：<br>
sudo vim ~/.bashrc<br><br>

>\#MAVEN<br>
export M2_HOME=/opt/maven/apache-maven-3.3.3<br>
export M2=$M2_HOME/bin<br>
export PATH=$M2:$PATH<br><br>

>然后 . ~/.bashrc   生效配置<br>
mvn -version  测试是否生效<br>

####安装配置ActiveMQ
>安装ActiveMQ<br>
sudo mkdir /opt/activemq/<br>
sudo mv ~/Downloads/apache-activemq-5.9.0-bin.tar.gz /opt/activemq/<br>
sudo tar zxvf apache-activemq-5.9.0-bin.tar.gz<br><br>

>配置ActiveMQ<br>
sudo chmod 755 activemq<br>
sudo ln -s activemq /usr/local/activemq<br>

>启动ActiveMQ(三种启动方式)<br>
1. 普通启动 ./activemq start<br>
2. 启动并指定日志文件 ./activemq start > /tmp/smlog<br>
3. 后台启动方式nohup ./activemq start > /tmp/smlog<br>
前两种方式下在命令行窗口关闭时或者ctrl+c时导致进程退出，采用后台启动方式则可以避免这种情况<br><br>

>注意事项：<br>
ActiveMQ对权限特别严格。可以运行activemq console来查看哪个文件夹权限不足。<br>
MQ暂时没有加入自启动，开机手动启动即可。<br><br>

####安装和配置mysql
>1.安装MySQL<br>
sudo apt-get install mysql-server mysql-client<br>
在安装的过程中会提示你输入Yes，然后会弹出root密码设置界面，这里可以先设置一个root密码作为登录mysql用户使用，<br>
之后需要的时候也可以运行mysqladmin -u root -p password进行修改密码。<br>
主要是sudo vim /etc/mysql/my.cnf下，注释掉binding-address=127.0.0.1<br><br>
*特别注意*：/etc/mysql/mysql.conf.d/mysqld.cnf  注释掉binding-address=127.0.0.1
不同的mysql可能默认安装的配置不一样。<br>
注意：/var/lib/mysql 目录的权限需要给放开。 chown -R mysql:mysql /var/lib/mysql<br>

>2.打开关闭服务<br>
打开关闭服务：/etc/init.d/mysql start/stop<br>
>3.MySQL配置(测试环境此处将root权限全部对外开启)<br>
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'admin' WITH GRANT OPTION; 所有的主机都能够远程访问该mac上的mysql。<br>
flush privileges。<br><br>
>查询mysql状态 <br>
/etc/init.d/mysql status<br>

####安装和配置mongod
>1.安装mongo<br>
sudo apt-get install mongodb<br>
2.配置mongo<br>
配置文件默认在 sudo vim /etc/mongodb.conf<br>
将#bind_ip = 127.0.0.1给注释掉<br>
将日志 权限打开 sudo chmod 777 /var/lib/mongodb<br>
sudo mkdir /data/db/<br>
sudo chmod 755 /data/db/<br>
>3.启动mongo<br>
/etc/init.d/mongodb start<br>
*开机自启动：update-rc.d mongodb defaults*<br>

####安装leiningen
>1.安装<br>
wget https://raw.github.com/technomancy/leiningen/preview/bin/lein<br>
sudo mv lein /usr/bin/<br>
chmod +x /usr/bin/lein<br><br>
运行lein命令<br>
下载https://github.com/technomancy/leiningen/downloads  jar包。<br>
sudo mv /opt/lein/leiningen-2.0.0-preview10-standalone.jar ~/.lein/self-installs/<br>
chmod 755 leiningen-2.0.0-preview10-standalone.jar<br>
修改/usr/bin/lein  Version修改成2.0.0-preview10<br>
然后运行lein等待下载依赖包即可<br>

####安装和配置git
>1.安装git<br>
sudo apt-get install git<br><br>

>2.配置git<br>
git config --global user.name = "eric******"<br>
git config --global user.email = "eric****@qq.com"<br>
ssh-keygen -C 'eric******@qq.com' -t rsa<br><br>

>~/.ssh/id_rsa.pub中的内容添加到github的sshkey中即可。<br>

>ssh -v git@github.com，输入密码测试是否畅通。<br>











