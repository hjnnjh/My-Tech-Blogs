#! https://zhuanlan.zhihu.com/p/672930120
# 在Win 11中安装WSL2(Windows Subsystem for Linux 2)

## 前言

- 手上有一张打游戏用的3080Ti，同时受不了课题组上古龟速GPU了；
- 需要一个小型的Linux炼丹炉，不想只装Ubuntu，有远程ssh的需求；
- 听说WSL无限接近原生Linux，想尝试一下。

## 安装步骤

### 准备工作

1. 最好是Win11系统；
2. 最好先把`Windows Terminal`装一下，关于`Windows Terminal`的安装（安装很简单哈，微软商店）和折腾，请参考我的另一篇文章：[Windows终端美化指南](https://github.com/hjnnjh/My-Tech-Blogs/blob/main/Windows终端美化指南.md)；

3. 最好配置一下`Windows Terminal`的代理，有一个很简单的方法，就是在Windows的系统环境变量中添加代理相关的环境变量：

   ![image-20231218211118930](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182111971.png)

### 安装WSL

1. 安装其实很简单，但有一些坑，详细步骤和选项请参考官方教程：[Install WSL](https://learn.microsoft.com/en-us/windows/wsl/install)；
2. 一般情况下，只需要在`Windows Terminal`输入一个命令就够了，默认安装的Linux版本是`Ubuntu 22.04`(2023.12.18)：

```powershell
wsl --install
```

3. 关于安装过程的坑

   - 首先你需要打开主板BIOS中的Hyper V（貌似是虚拟化技术）支持，以华硕主板为例，需要修改图中红圈选项为`enable`，否则后面安装完成打不开`Ubuntu`实例：

     ![image-20231218211227570](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182112599.png)

   - 安装完成之后首次启动大概率会遇到一个与网络相关的warning信息，我们后面会在实例内单独配置代理，所以这个信息我们通过配置文件`%UserProfile%\.wslconfig`将其忽略掉，修改这个文件的内容，然后重启`wsl`：

     ```powershell
     [experimental]
     autoProxy=false  # 修改%UserProfile%\.wslconfig
     ```

     ```powershell
     wsl --shutdown
     ```

4. WSL的迁移，WSL默认安装在C盘，如需要将实例迁移至非系统盘，参考如下步骤：

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

   - 使用导出的文件进行导入即可(`--version 2`可以不加，现在默认都是WSL2)

     ```powershell
     wsl --import <instance_name> D:\import\ D:\export.tar --version 2
     ```

5. 至此，WSL安装完成，可以自己配置一下`Windows Terminal`中WSL窗口的外观效果如下：

   ![image-20231218204950177](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182049202.png)

## 配置ssh登录、终端美化、深度学习环境的配置

### 配置ssh登录、终端代理、内网穿透

- 参考我的文章：[云服务器配置指南（同时适用于Ubuntu系统的电脑或者GPU服务器）](https://zhuanlan.zhihu.com/p/672920221)。

### 终端美化：安装zsh, oh-my-zsh, powerlevel10k

- 参考我的文章：[云服务器配置指南（同时适用于Ubuntu系统的电脑或者GPU服务器）](https://zhuanlan.zhihu.com/p/672920221)。

- **特别注意！！！**配置完成后，**使用ssh登录**，打开`~/.zshrc`，在后面添加：

  ```bash
  export PATH=/usr/lib/wsl/lib:$PATH
  ```

  然后让配置文件生效：

  ```bash
  source ~/.zshrc
  ```

  一定要记得操作这一步，不然`nvidia-smi`是无效的。

### 深度学习环境的配置

1. **特别注意！！！**WSL中不需要安装NVIDIA GPU驱动程序，因为WSL使用的是Windows端的驱动程序，同时甚至不需要安装CUDA、cudnn之内的工具包，首先现在的深度学习框架安装脚本会自动在环境中安装这些工具包，其次，我更推荐用`docker`配置深度学习环境，这个需要的是[NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)，关于在`docker`中配置深度学习环境，后续更新。

2. 安装`Miniconda`

   请参考教程：[在Linux中安装Miniconda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html)，有一点需要注意就是在`zsh shell`中，执行脚本命令是通过下面的方式执行：

   ```bash
   sh Miniconda3-latest-Linux-x86_64.sh
   ```

3. 然后就算是基本上配置完成啦，速度的话，我使用`jax`实测了一下，感觉能有原生Ubuntu的90%，附上几张截图：

   ![image-20231218210717916](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182107951.png)

   ![image-20231218210700049](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202312182107078.png)

## 杂项

有问题，欢迎大家多多评论或者直接私信问我～