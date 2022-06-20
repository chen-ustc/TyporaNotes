I have done some change
# ssh的免密登录配置

<font color='greeny'>这个方法对于windows和linux操作系统都是适用的</font>

## 单层ssh配置

- 进入.ssh目录

  .ssh目录无论是windows还是linux都在用户的根目录下

- 生成本地PC端密钥

  `ssh-keygen -t rsa`生成本地的私钥和公钥

- 将rsa_pub中的内容添加到服务器端的authorized_keys中

  - scp上传,然后`cat rsa_pub >> authorized_keys`
  - 复制粘贴

- 修改authorized_keys权限为600

​	`chmod 600 authorized_keys`

- 修改.ssh文件夹的权限为700

  `chmod 700 .ssh`

然后就可以通过cmd或者wsl或者terminal进行免密登录了.

## 双层ssh内网穿透

在单层的基础上需要修改配置文件,使用代理

另外还需要对内网主机的suthorized_keys进行修改,同时也需要修改.ssh目录和authorized_keys的权限.

参考博文:

https://blog.csdn.net/qq_38476684/article/details/100028507
