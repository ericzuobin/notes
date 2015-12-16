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
