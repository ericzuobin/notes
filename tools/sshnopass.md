##ssh无密码登陆

> 原理和github的密匙一样

1. 在你的自己的机器下面使用ssh-keygen命令来实现创建公钥
使用 ssh-keygen -t rsa 来创建密钥，程序会问你存放的目录，回车两次即可

2. 将你~/.ssh目录中的id_rsa.pub这个文件拷贝到你要登录的服务器，然后再运行以下命令来将公钥导入到~/.ssh/authorized_keys这个文件中
cat id_rsa.pub >> ~/.ssh/authorized_keys，注意是管道符是 >>

>另外如果不行，试着修改下权限
~/.ssh权限设置为700
~/.ssh/authorized_keys的权限设置为600

完毕之后，退出服务器的登录，再使用ssh登录，你就会发现服务器不会再向你询问密码了.

DangDangDangDang！！！

如果,如果你觉得上面还是太麻烦,或者你有N多台服务器想要设置无密码登陆。下面

创建一个ip.txt的文档，里面包含远程主机登陆ip或者用户名@ip

~~~sh
function Echo() {
	parameter=$1
	name=$2
	if [ $1 -ne 1 ]; then
        	echo "Usage:  $2  ip.txt"
        	echo "        please input ip.txt for freelogin and password next"
        	exit 1
	fi      
}

function LocalSsh() {
	remote=$1
	echo "------------------------------------------------------------"
	echo "Begin to set Local-Remote free login!"
	ssh-keygen -t rsa -P ''
	scp /root/.ssh/id_rsa.pub  $1:/root/id_rsa.pub
	ssh $1 'cat /root/id_rsa.pub >> /root/.ssh/authorized_keys'
	ssh  $1 'chmod 600  /root/.ssh/authorized_keys'
	echo "Local-Remote free login set Ok!"
	echo "------------------------------------------------------------"
}

if [ $# -eq 1 ]; then
	num=`awk 'END{print NR}' $1`
	echo 'Set free login ip.txt:'

	for ((i=1;i<=$num;i++)); do
		ip=`cat $1 | sed -n ''$i'p'`
		echo $ip
		LocalSsh $ip
	done
else
	Echo $# $0
fi
~~~
