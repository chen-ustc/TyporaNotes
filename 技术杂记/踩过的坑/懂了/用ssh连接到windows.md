2022年5月19日10:55:25

---



# 在linux系统下用ssh连接到windows

在最近的工作过程中，发现在windows系统下用ssh连接linux非常简单，但是在linux系统下用ssh连接windows却行不通，要么timed out，要么connection refused。其原因是因为win11默认openssh服务是不开启的，即默认不安装openssh服务端。

**第一步：安装openssh服务端**

![image-20220519110020149](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220519110020149.png)

![image-20220519110050282](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220519110050282.png)

在`设置->应用->可选功能`中安装openssh客户端。

**第二步：启动ssh服务**

启动ssh服务需要用管理员身份启动cmd或者powershell，可以先启动普通用户的powershell，然后键入命令`start-process PowerShell -verb runas`提升权限等级。

在打开管理员身份的powershell界面后，键入`net start sshd`启动sshd服务。

**第三步：连接windows**

启动成功后，就可以用ssh命令来连接windows了，但需要注意的是，输入的用户名和密码是登录的microsoft账户的用户名和密码，而非pin码。然后就可以连接成功了。

连接成功后的页面是windows的cmd控制台页面，linux的命令行是不能用的，需要用dos命令来进行操作。