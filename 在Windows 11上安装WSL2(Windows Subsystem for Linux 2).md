#! https://zhuanlan.zhihu.com/p/672930120

# 在 Win 11 中安装 WSL2(Windows Subsystem for Linux 2)

## 前言

- 手上有一张打游戏用的 3080Ti，同时受不了课题组上古龟速 GPU 了；
- 需要一个小型的 Linux 炼丹炉，不想只装 Ubuntu，有远程 ssh 的需求；
- 听说 WSL 无限接近原生 Linux，想尝试一下。

## 安装步骤

### 准备工作

1. 最好是 Win11 系统；
2. 最好先把`Windows Terminal`装一下，关于`Windows Terminal`的安装（安装很简单哈，微软商店）和折腾，请参考我的另一篇文章：[Windows 终端美化指南](https://github.com/hjnnjh/My-Tech-Blogs/blob/main/Windows终端美化指南.md)；

3. 最好配置一下`Windows Terminal`的代理，有一个很简单的方法，就是在 Windows 的系统环境变量中添加代理相关的环境变量(端口号记得修改)，这样设置是一定能让终端走代理的，不过这样做有个问题，设置之后我的奥创中心(我是 ROG 的主板)无法正常识别设备了，找了一晚上原因最后发现罪魁祸首就是添加了这两个环境变量，建议配置完成之后把这两个环境变量删除：

   ![Windows系统环境变量中新建proxy变量](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182111971.png)

### 安装 WSL

1. 安装其实很简单，但有一些坑，详细步骤和选项请参考官方教程：[Install WSL](https://learn.microsoft.com/en-us/windows/wsl/install)；
2. 一般情况下，只需要在`Windows Terminal`输入一个命令就够了，默认安装的 Linux 版本是`Ubuntu 22.04`(2023.12.18)：

```powershell
wsl --install
```

3. 关于安装过程的坑

   - 首先你需要打开主板 BIOS 中的虚拟化技术支持，以华硕主板为例，需要修改图中红圈选项为`enable`，否则后面安装完成打不开`Ubuntu`实例：

     ![修改BIOS选项](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182112599.png)

   - 安装完成之后首次启动大概率会遇到一个与网络相关的 warning 信息，我们后面会在实例内单独配置代理，所以这个信息我们通过配置文件`%UserProfile%\.wslconfig`将其忽略掉，修改这个文件的内容，然后重启`wsl`：

     ```powershell
     [experimental]
     autoProxy=false  # 修改%UserProfile%\.wslconfig
     ```

     ```powershell
     wsl --shutdown
     ```

4. WSL 的迁移，WSL 默认安装在 C 盘，如需要将实例迁移至非系统盘，参考如下步骤：

   - 查看正在运行的实例

     ```powershell
     wsl -l -v
     ```

   - 终止正在运行的实例

     ```powershell
     wsl --terminate <instance_name>
     ```

   - 将需要迁移的实例，进行导出，导出路径自定义

     ```powershell
     wsl --export <instance_name> D:\export.tar
     ```

   - 导出完成之后，就需要将原有的实例进行卸载

     ```powershell
     wsl --unregister <instance_name>
     ```

   - 使用导出的文件进行导入即可(`--version 2`可以不加，现在默认都是 WSL2)

     ```powershell
     wsl --import <instance_name> D:\import\ D:\export.tar --version 2
     ```

5. 至此，WSL 安装完成，可以自己配置一下`Windows Terminal`中 WSL 窗口的外观效果如下：

   ![安装完成的效果](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182049202.png)

## 配置 ssh 登录、终端美化、深度学习环境的配置

### 配置 ssh 登录、终端代理、内网穿透

- 参考我的文章：[云服务器配置指南（同时适用于 Ubuntu 系统的电脑或者 GPU 服务器）](https://zhuanlan.zhihu.com/p/672920221)。

### 终端美化：安装 zsh, oh-my-zsh, powerlevel10k

- 参考我的文章：[云服务器配置指南（同时适用于 Ubuntu 系统的电脑或者 GPU 服务器）](https://zhuanlan.zhihu.com/p/672920221)。

- **特别注意！！！** 配置完成后，**使用 ssh 登录**，打开`~/.zshrc`，在后面添加：

  ```bash
  export PATH=/usr/lib/wsl/lib:$PATH
  ```

  然后让配置文件生效：

  ```bash
  source ~/.zshrc
  ```

  一定要记得操作这一步，不然`nvidia-smi`是无效的。

### 深度学习环境的配置

1. **特别注意！！！** WSL 中不需要安装 NVIDIA GPU 驱动程序，因为 WSL 使用的是 Windows 端的驱动程序，同时甚至不需要安装 CUDA、cudnn 之内的工具包，首先现在的深度学习框架安装脚本会自动在环境中安装这些工具包，其次，我更推荐用`docker`配置深度学习环境，这个需要的是[NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)，关于在`docker`中配置深度学习环境，后续更新。

2. 安装`Miniconda`

   请参考教程：[在 Linux 中安装 Miniconda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html)，有一点需要注意就是在`zsh shell`中，执行脚本命令是通过下面的方式执行：

   ```bash
   sh Miniconda3-latest-Linux-x86_64.sh
   ```

3. 然后就算是基本上配置完成啦，速度的话，我使用`jax`实测了一下，感觉能有原生 Ubuntu 的 90%，附上几张截图：

   ![正在跑一个我自己用JAX写的DEMO](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182107951.png)

   ![美化后的效果与配置，主题是zsh下的p10k](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182107078.png)

## 杂项

1. 配置 WSL 开机自启

   - 在任意目录下(我的目录是`%USERPROFILE%\.shell\`)新建文件 `wsl-startup.vbs`；
   - 在 `wsl-startup.vbs` 中填充如下内容，其中`<instance_name>`为你的实例名称：

   ```powershell
   set ws=wscript.CreateObject("wscript.shell")
   ws.run "wsl -d <instance_name>", 0
   ```

   - 搜索`任务计划程序`，打开该程序；
   - 在 Windows 中新建一个`基本计划任务`，在`触发器`中选择`启动时`，在`操作`中选择`启动程序`为`wscript.exe`，在`程序或脚本`中填入`wscript.exe`的绝对路径，在`添加参数（可选）`中填写`wsl-startup.vbs`的绝对路径`"%USERPROFILE%\.shell\wsl-startup.vbs"`(建议加双引号)，然后点击`确定`即可（之后补图片）。
   - 修改这个任务的属性，勾选`不需要用户登录`，这样就可以**在不登录系统的情况下**实现 WSL 实例的开机自启。
   - **本人实测，当电脑出现异常断电重启时，WSL 实例仍然会启动，但是通过其他方式配置的开机自启，比如将`vbs`脚本加入`shell:startup`目录下，WSL 实例是不会启动的。**
   - 2024.03.04更新，`基本计划任务`有时候会莫名其妙出BUG，导致任务无法正常执行，不过我找到了解决方案：删除原来的基本计划任务，重新新建一个`计划任务`,`触发器`中设置`延迟30秒启动`,`启动程序`不要用脚本了，直接用`wsl.exe`，路径为`C:\Windows\System32\wsl.exe`，其他设置不变。

2. 使用远程开机卡实现 WSL 异常时的远程重启（**推荐**）

   - 淘宝上搜一下很多这种接入了米家或者 Apple Homekit 的开机卡，原理是接管了主板的开机跳线；

     ![开机卡](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312211658933.webp)

   - 这样就可以实现远程操作电脑正常开机，然后再通过上面的方法实现 WSL 的开机自启。

3. 使用小米智能插座实现 WSL 异常时的远程重启（**不推荐，我建议别打电源的主意，不然数据损坏了欲哭无泪**）

   - BIOS 打开主板的上电自启功能；
   - 智能插座联网，在手机 APP 上控制智能插座开关就能实现 WSL 的重启。

有问题，欢迎大家多多评论或者直接私信问我～
