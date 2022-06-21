[toc]
# 基于WSL2在Windows下安装Linux
## 简介
WSL(Windows SUbsystem for Linux)是微软在提出拥抱开源后推出的可以在Windows下安装的Linux子系统.简单来理解,可以认为是一个更加轻量级,速度更快,没有图形界面的VMware虚拟机.WSL2相较于WSL改善了许多之前不足的地方,更加方便好用.因此这里在安装Linux发行版时,会将WSL2设置为默认版本.
## 预备工作

1. 在`设置-应用与功能-程序与功能-启用或关闭Windows功能`中勾选以下两个选项:

   ![](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220622002709916.png)

   如果你使用的是win11,那么在设置中可能找不到程序与功能这一选项,这时需要在`控制面板-程序-程序与功能-启用或关闭windows功能`中进行勾选.

2. 启用"适用于Linux的Windows子系统"可选功能

   以管理员身份打开power shell,并输入命令`dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart`,然后回车

3. 启用"虚拟机平台"可选功能

   同样,以管理员身份打开power shell,输入以下命令并回车:`dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart`

   2和3若成功,会显示如下图所示结果:

   ![image-20220622004410551](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220622004410551.png)

4. 将WSL2设置为默认版本

   在管理员身份的power shell中输入`wsl --set-default-version 2`并回车
   
5. 重启电脑

## 安装过程和结果

在Microsoft store中搜索ubuntu,从搜索结果中选择你想安装的版本并点击安装即可.安装过程中会提示你创建用户,按照提示进行操作即可完成子系统的安装.另外,微软商店中的这些发行版都是没有图形界面的,因此只能用命令行来操作子系统.

> 以上内容参考:
>
> https://wenku.baidu.com/view/4562b4558d9951e79b89680203d8ce2f00666565.html

另外,我在安装的过程中,在最后定义用户名时出现了`参考的对象类型不支持尝试的操作`的报错,主要是因为使用代理软件导致winsock出现了问题,解决方法为:

创建一个.reg后缀的脚本文件,其内容编辑为:

```
Windows Registry Editor Version 5.00
 
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WinSock2\Parameters\AppId_Catalog\0408F7A3]
"AppFullPath"="C:\\Windows\\System32\\wsl.exe"
"PermittedLspCategories"=dword:80000000
```

双击执行,即可正常进入系统.

> 此部分内容参考:
>
> https://blog.csdn.net/marin1993/article/details/119841299

## 文档

更多WSL相关的信息和操作请查阅:

> https://docs.microsoft.com/zh-cn/windows/wsl/