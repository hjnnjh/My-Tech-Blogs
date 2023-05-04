# 如果使用Homebrew更新formula或者cask之后想回退到以前的版本

- 使用命令查看homebrew仓库中对应application的安装脚本`.rb`文件

  ```shell
  brew info xxx(formula/cask)
  ```

  这里以Vscode为例，如图所示：

  ![image-20230504132947337](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202305041329375.png)

- 进入GitHub仓库之后，点击`history`查看历史版本的安装脚本文件：

  ![image-20230504133559057](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202305041335090.png)

- 点击`View at this point in the histroy`

  ![image-20230504133741137](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202305041337159.png)

- 下载安装脚本文件到本地

  ![image-20230504133835535](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202305041338561.png)

- 使用以下命令进行重新安装

  ```shell
  brew uninstall --cask <cask_name>
  brew install --cask <path_to_ruby_file>
  ```

  