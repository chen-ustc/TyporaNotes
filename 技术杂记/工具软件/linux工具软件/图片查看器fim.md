## FIM的安装

FIM图像查看器在基于DEB的系统（例如Ubuntu，Linux Mint）的默认存储库中可用，因此，可以使用如下命令安装fim：

```shell
sudo aot-get install fim
```

如果你的Linux发行版的默认存储库中没有它，那么您可以从源代码下载、编译和安装，可以按照官方文档页面中“下载和构建说明”一节所述进行操作。

## FIM的使用

**最基础的命令：**

```shell
fim xxx.jpg
```

用于查看某张图片。

**可选参数**

```
fim -a xxx.jpg //autozoom，自动缩放
fim -t xxx.jpg //用ASCII码的形式来显示图片
fim -a * //可以使用通配符来显示多张图片
```

**fim窗口管理快捷键**

- b（before）：上一张图片
- n（next）：下一张图片 
- +/-：放大/缩小
- f（flip）：翻转
- m（mirror）：镜像
- r（round）：旋转，每次旋转$45^{\circ}$。
- q(quit)/esc：退出
