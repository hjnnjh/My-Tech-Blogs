# Charles抓包工具更新之后需要注意的东西

更新Charles之后，一定要记得重新安装根证书，把原先钥匙串访问中的`Charles Proxy CA...`根证书删除，再通过下面的选项重新安装新的根证书：

![image-20230507155417848](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202305071554897.png)

安装完成之后将根证书改为`始终信任`