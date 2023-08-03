# Windows终端美化指南

## 自定义Windows Terminal

参考以下教程：[自定义Windows Terminal](https://sspai.com/post/59380)

- 我主要修改配置文件中`default`中的内容（字体、亚克力背景等）

## 安装Oh-My-Posh

- 参考官网给出的[安装教程](https://ohmyposh.dev/docs/installation/windows)

- 安装自动补全插件以及配置：参考这个[教程](https://juejin.cn/post/7139514254801141791)

- 中间会遇到一些报错信息，比如`ps1`脚本不能加载，需要修改脚本执行权限：

  ```powershell
  Set-ExecutionPolicy RemoteSigned
  ```

- 按照官网的[安装教程](https://ohmyposh.dev/docs/installation/windows)选择自己喜欢的主题