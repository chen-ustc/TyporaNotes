scp means openssh secure file copy 

it is used to copy files between local host to remote or remote to remote

following are the arguments often used and the example of format of command

- -4: forces scp to use IPV4 addresses only
- -6: forces scp to use IPV6 addressed only
- -r: recursively copy entire directories.

**本地复制文件到远程**

```shell
scp local_file remote_username@remote_ip:remote_folder
scp local_file remote_username@remote_ip:remote_filename
scp local_file remote_ip:remote_folder
scp local_file remote_ip:remote_filename
```



如果输入了用户名，那么只需要输入密码。

如果只有ip地址，那么可能需要输入用户名和密码。

**本地复制文件夹到远程**

```
scp -r local_folder remote_username@remote_ip:remote_folder
scp -r local_folder remote_ip:remote_folder
```

**交换冒号两边内容的顺序可以实现从远端复制到本地**

使用scp命令要确保用户具有可读取远端服务器相应文件的全县，否则scp命令是无法起作用的

