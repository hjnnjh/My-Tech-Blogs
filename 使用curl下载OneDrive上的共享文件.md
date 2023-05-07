# 使用`curl`下载Onedrive上的共享文件

OneDrive现在的共享文件链接格式如下：

```shell
https://1drv.ms/f/s!...
```

不能直接使用`curl`，`wget`这类工具下载，要想通过命令行进行下载，首先我们要在浏览器中抓包，`F12`打开开发者控制台，点击`下载`，找到图中所示的这个包：

![image-20230507173039922](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202305071730964.png)

复制该请求的`curl`格式链接：

![image-20230507173123164](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202305071731187.png)

在复制得到的命令后加上以下参数：

```shell
curl ... -o "xxx.zip"
```

即可在使用`curl`下载OneDrive中的共享文件（用来下载大文件比如数据集到服务器上很方便）。