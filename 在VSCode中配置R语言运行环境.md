#! https://zhuanlan.zhihu.com/p/650675370

# 在 VSCode 中配置 R 语言运行环境

## 安装 VSCode 和 R

由于我的电脑是 Mac，这里我使用`Homebrew`的方式进行安装，`Homebrew`的配置请参考[官网教程](https://brew.sh)，如果电脑是 Windows，也可以使用类似的工具比如`winget`进行安装。

```shell
brew install --cask vscode
brew install r
```

一般情况下，不需要额外配置`R`的编译器，默认使用 Mac Command Line Tool 自带的`clang`就行，如果你想使用`gcc`，注意版本问题（比如目前`gcc-13`就有 Bug 但`gcc-12`是没问题的），配置的话需要在`~/.R/Makevars`文件中进行配置，举个例子：

```shell
CC=/opt/homebrew/bin/gcc-12
CCX=/opt/homebrew/bin/g++-12
```

## 安装`radian`

`radian`是一个更好用的 R Console，提供了更全面的如自动补全功能（而且比 Native R 好看哈），[官方仓库](https://github.com/randy3k/radian)，个人推荐安装。

参考官方仓库里给出的安装步骤进行安装，需要注意的是，如果你已经安装了`conda`，比如我的`conda`是用`Homebrew`安装的，一般情况下，考虑新建一个环境，比如叫`R`，在这个新环境中使用下面的命令安装`radian`：

```shell
pip3 install -U radian
```

将别名加入`~/.zshrc`环境变量配置文件中：

```shell
echo 'alias r="radian"' >> ~/.zshrc && source ~/.zshrc
```

现在我们使用下面两个命令中的任意一个，就能启动`radian`了：

```shell
r
radian
```

![image-20230817164955611](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202308171649697.png)

## 在 VSCode 中配置 R 运行环境

### 在 R 中安装 languageserver

```R
install.packages("languageserver")
```

### 安装 R Extension for VSCode

![image-20230817165320301](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202308171653334.png)

### 安装 R Debugger Extension

![image-20230817165514027](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202308171655057.png)

### 配置 radian 作为默认的 R Interactive Console

- 在设置中勾选`r.bracketedPaste`

- 在设置中配置`radian`的路径`r.rpath.windows/mac/linux`，比如通过`Homebrew conda`环境中的`pip3`安装的`radian`路径如下：

```shell
/opt/homebrew/bin/radian
```

### 安装`httpgd`

简单来说，`httpgd`是一个用于在 VSCode 中展示更清晰图像的 package，安装：

```
install.packages("httpgd")
```

使用教程请参考：[Geting Started](https://nx10.github.io/httpgd/articles/a01_how-to-get-started.html)。

勾选 VSCode 中的`Use Httpgd`选项：

![image-202310061402405](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202310061402405.png)

如需手动在 radian 中启动 Server 并在浏览器中打开：

```shell
hgd()
hgd_browse()
```

在 radian 中关闭 Server：

```shell
dev.off()
```

## 杂项

可以对`radian`和`R extension` 进行自定义配置，需要自己新建配置文件，User 配置文件位置应为：

```shell
~/.Rprofile
```

这里分享一下我的`~/.Rprofile`，主要为`VSCode R Debugger`的配置：

```shell
if(Sys.getenv('VSCODE_DEBUG_SESSION')=='1'){
	Sys.setenv(TERM_PROGRAM='vscode')
  source(file.path(Sys.getenv(if (.Platform$OS.type == "windows") "USERPROFILE" else "HOME"), ".vscode-R", "init.R"))
	options(vsc.plot = FALSE)
	options(vsc.use_httpgd = FALSE)
}
```

上述配置允许在使用`VSCode R Debugger`调试R代码时，可以在VSCode内部预览`(View)`断点之前的数据变量（如`dataframe`），同时在调试模式下，禁用`httpgd`，可以在断点前用`print(p)`预览图像。
