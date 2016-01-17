##添加一个用户

adduser sysmgr

passwd sysmgr

##禁止root账户ssh登陆

vim /etc/ssh/sshd_config

PermitRootLogin yes

修改为：

PermitRootLogin no

##添加登录超时

用户在线30分钟无操作则超时断开连接，在/etc/profile中添加：

export TMOUT=18000
readonly TMOUT

##SSH免密码登陆配置

cat id_rsa.pub >> ~/.ssh/authorized_keys，

注意是管道符是 >>

>另外如果不行，试着修改下权限

>~/.ssh权限设置为700

>~/.ssh/authorized_keys的权限设置为600


##禁止非root用户执行/etc/rc.d/init.d/下的系统命令

chmod -R 700 /etc/rc.d/init.d/*

chmod -R 777 /etc/rc.d/init.d/* #恢复默认设置

##删除不需要的软件包

yum remove Deployment_Guide-en-US finger cups-libs cups bluez-libs desktop-file-utils ppp rp-pppoe wireless-tools irda-utils nfs-utils nfs-utils-lib rdate fetchmail eject ksh mkbootdisk mtools syslinux tcsh startup-notification talk apmd rmt dump setserial portmap yp-tools ypbind

yum remove telnet rsh ftp rcp

##服务器禁止ping

cp /etc/rc.d/rc.local /etc/rc.d/rc.localbak

vim /etc/rc.d/rc.local #在文件末尾增加下面这一行

echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
　
参数0表示允许 1表示禁止


##限制登录失败次数并锁定

在/etc/pam.d/login后添加

auth required pam_tally2.so deny=6 unlock_time=180 even_deny_root root_unlock_time=180

减少history命令记录

##现在命令历史记录数
执行过的历史命令记录越多，从一定程度上讲会给维护带来简便，但同样会伴随安全问题

vim /etc/profile

找到 HISTSIZE=1000 改为 HISTSIZE=50。

##开启iptables配置

- 关闭firewall：
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）

- 安装iptables防火墙

yum install iptables-services #安装

vim /etc/sysconfig/iptables #编辑防火墙配置文件
iptables防火墙（这里iptables已经安装，下面进行配置）

~~~BASH
##第一种
# sampleconfiguration for iptables service
# you can edit thismanually or use system-config-firewall
# please do not askus to add additional ports/services to this default configuration
*filter
:INPUT DROP [0:0]
:FORWARD ACCEPT[0:0]
:OUTPUT ACCEPT[0:0]
-A INPUT -m state--state RELATED,ESTABLISHED -j ACCEPT
#ping
-A INPUT -p icmp -jACCEPT
#回环地址
-A INPUT -i lo -jACCEPT
#开放端口
-A INPUT -p tcp -mstate --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -jACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 8080-j ACCEPT
-A INPUT -j REJECT--reject-with icmp-host-prohibited
-A FORWARD -jREJECT --reject-with icmp-host-prohibited
COMMIT

##第二种
*filter
:INPUT DROP [0:0]
:FORWARD ACCEPT[0:0]
:OUTPUT ACCEPT[0:0]
-A INPUT -m state --state ESTABLISHED -j ACCEPT
#回环地址
-A INPUT -i lo -j ACCEPT
#开放端口
-A INPUT -p tcp -m tcp --dport 22 -j ACCEPT   #SSH端口最好修改为高位端口
-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
-A INPUT -p tcp -m tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT
-A OUTPUT -p tcp -m tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
COMMIT
:wq! #保存退出
~~~

systemctl restart iptables.service
 #最后重启防火墙使配置生效

systemctl enable iptables.service #设置防火墙开机启动

iptables -L  查询已生成的


##修改SSH端口号

1. 修改/etc/ssh/sshd_config
vi /etc/ssh/sshd_config
\#Port 22         //这行去掉#号
Port 20000      //下面添加这一行

2. 修改SELinux
使用以下命令查看当前SElinux 允许的ssh端口：
semanage port -l | grep ssh

添加20000端口到 SELinux
semanage port -a -t ssh_port_t -p tcp 20000

然后确认一下是否添加进去
semanage port -l | grep ssh
如果成功会输出
ssh_port_t                    tcp    20000, 22

3. 重启ssh
systemctl restart sshd.servic

4. iptables中打开20000端口即可


##禁用不使用的用户

注意：不建议直接删除，当你需要某个用户时，自己重新添加会很麻烦。也可以 usermod -L 或 passwd -l user 锁定。

cp /etc/passwd{,.bak} 修改之前先备份

vi /etc/passwd 编辑用户，在前面加上#注释掉此行

~~~BASH
注释的用户名：
# cat /etc/passwd|grep ^#
#adm:x:3:4:adm:/var/adm:/sbin/nologin
#lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
#shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
#halt:x:7:0:halt:/sbin:/sbin/halt
#uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
#operator:x:11:0:operator:/root:/sbin/nologin
#games:x:12:100:games:/usr/games:/sbin/nologin
#gopher:x:13:30:gopher:/var/gopher:/sbin/nologin
#ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
#nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
#postfix:x:89:89::/var/spool/postfix:/sbin/nologin
注释的组：
# cat /etc/group|grep ^#
#adm:x:4:adm,daemon
#lp:x:7:daemon
#uucp:x:14:
#games:x:20:
#gopher:x:30:
#video:x:39:
#dip:x:40:
#ftp:x:50:
#audio:x:63:
#floppy:x:19:
#postfix:x:89:
~~~

##增强特殊文件权限

给下面的文件加上不可更改属性，从而防止非授权用户获得权限

~~~BASH
chattr +i /etc/passwd
chattr +i /etc/shadow
chattr +i /etc/group
chattr +i /etc/gshadow
chattr +i /etc/services #给系统服务端口列表文件加锁，防止未经许可的删除或添加服务
chattr +i /etc/pam.d/su
chattr +i /etc/ssh/sshd_config
显示文件的属性

lsattr /etc/passwd /etc/shadow /etc/services /etc/ssh/sshd_config
lsattr /etc/passwd /etc/passwd /etc/shadow /etc/services /etc/ssh/sshd_config /etc/group
注意：执行以上 chattr 权限修改之后，就无法添加删除用户了。

如果再要添加删除用户，需要先取消上面的设置，等用户添加删除完成之后，再执行上面的操作，例如取消只读权限chattr -i /etc/passwd。（记得重新设置只读）

如果需要重新可修改执行以下即可:
chattr -i /etc/passwd  /etc/group  /etc/shadow /etc/gshadow /etc/services /etc/pam.d/su /etc/ssh/sshd_config
~~~

##禁止Control-Alt-Delete组合键重启系统
在linux的默认设置下，同时按下Control-Alt-Delete键，系统将自动重启，这是很不安全的，因此要禁止Control-Alt-Delete组合键重启系统，只需修改/etc/inittab文件：

[root@localhost ~]#vi /etc/inittab

找到此行：ca::ctrlaltdel:/sbin/shutdown -t3 -r now

在之前加上“#”

然后执行：

[root@localhost ~]#telinit q



##防止IP欺骗

编辑host.conf文件并增加如下几行来防止IP欺骗攻击。

order bind，hosts

multi off

nospoof on


##防止DoS攻击

对系统所有的用户设置资源限制可以防止DoS类型攻击，如最大进程数和内存使用数量等。

可以在/etc/security/limits.conf中添加如下几行：
~~~
*    soft    core    0
*    soft    nproc   2048
*    hard    nproc   16384
*    soft    nofile 1024
*    hard    nofile  65536
~~~

core 0 表示禁止创建core文件；nproc 128 把最多的进程数限制到20；nofile 64 表示把一个用户同时打开的最大文件数限制为64；* 表示登录到系统的所有用户，不包括root

然后必须编辑/etc/pam.d/login文件检查下面一行是否存在。

session    required     pam_limits.so
limits.conf参数的值需要根据具体情况调整。


##关闭系统不需要的服务
~~~
service acpid stop chkconfig acpid off #停止服务，取消开机启动 #电源进阶设定，常用在 Laptop 上
service autofs stop chkconfig autofs off #停用自动挂载档桉系统与週边装置
service bluetooth stop chkconfig bluetooth off #停用Bluetooth蓝芽
service cpuspeed stop chkconfig cpuspeed off #停用控制CPU速度主要用来省电
service cups stop chkconfig cups off #停用 Common UNIX Printing System 使系统支援印表机
service ip6tables stop chkconfig ip6tables off #禁止IPv6

如果要恢复某一个服务，可以执行下面操作
service acpid start chkconfig acpid on
~~~


##安装Mysql

~~~BASH
yum install -y mysql-server mysql mysql-deve
sudo rpm -Uvh http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
Install MySQL Packages
MySQL server can now be installed using YUM. The MySQL client package will be included with the server package.

sudo yum -y install mysql-community-server
The MySQL daemon should be enabled to start on boot.

sudo /usr/bin/systemctl enable mysqld
The server can now be started.

sudo /usr/bin/systemctl start mysqld

出现“mysql>”提示符后输入：
mysql> update user set password = Password('root') where User = 'root';
回车后执行（刷新MySQL系统权限相关的表）：
mysql> flush privileges;
再执行exit退出：
mysql> exit;
退出后，使用以下命令登陆mysql，试试是否成功：
#mysql -u root -p
按提示输入密码：root
~~~

systemctl enable php-fpm
systemctl start php-fpm
