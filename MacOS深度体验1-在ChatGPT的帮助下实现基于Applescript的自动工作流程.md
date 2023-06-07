#! https://zhuanlan.zhihu.com/p/635449456
# MacOS进阶使用第1期：ChatGPT + Applescript实现自动工作流程

## 为什么要折腾

最近写博士论文的开题报告，文献综述太麻烦了，就想着稍微偷偷懒:），我负责找文献，Bingchat负责帮我总结文献，然后我再改改就可以直接放到文献综述部分了。但是在实际操作的时候，Zotero文献库中的文献实在是太多了，然后我的Zotero中设置的PDF文件的默认打开方式不是Edge(因为在Edge中复制PDF文本不能自动处理分行)，所以每次都需要手动右键点击PDF，选择打开方式，把文献在Edge中打开，我反正是觉得非常的麻烦，理想中的最便捷的方式应该是这样的：

- 选中Zotero中的条目；
- 写一个Applescript，绑定一个键盘快捷键，直接用快捷键将选中条目中的PDF文件在Edge中打开；
- 更进一步，如果你用的是罗技的G系列或者MX系列的鼠标，还可以针对Zotero设置特定于应用的宏，然后就可以直接将快捷键绑定到鼠标侧键，不要太方便好吧。

但是Zotero现在还不支持Applescript，所以就只能退而求其次，我通过Zotero打开条目中PDF文件的位置，然后再使用Applescript打开。但是我不会写Applescript啊😢，那就要请ChatGPT帮我写了😄

## 用ChatGPT生成Applescript脚本
- Prompt

```
在Mac中如何使用apple script实现将选中文件在Edge中打开？
```

- Output

```applescript
on run
    tell application "Finder"
        set selectedFiles to selection as alias list
    end tell
    
    repeat with theFile in selectedFiles
        tell application "Finder"
            set fileToOpen to POSIX path of (theFile as string)
        end tell
        
        tell application "Microsoft Edge"
            activate
            open location "file://" & fileToOpen
        end tell
    end repeat
end run
```

## 进一步完善
上面的Applescript脚本一开始是没啥问题的，但是用着用着就会出现`拒绝访问文件`的报错，一通分析之后发现是因为我的Mac上有两个版本的`Microsoft Edge`，然后上面那个脚本不区分`Microsoft Edge`的版本，用低版本的`Edge`打开PDF文件有概率出现`拒绝访问文件`的问题。

<img src="https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202306072121705.png" alt="image-20230607212155642" style="zoom:50%;" />

不过Applescript为我们提供了选择最新版本`Edge`打开PDF文件的实现方法，这时候又要靠ChatGPT了🐶

- Prompt

```
我的Mac上Microsoft Edge有两个版本，我想在AppleScript中实现将选中文件在最新版本的Edge中打开
```

- Output

```applescript
on run
    tell application "Finder"
        set selectedFiles to selection as alias list
    end tell
    
    set edgePath to getLatestEdgePath()
    if edgePath is not equal to "" then
        repeat with theFile in selectedFiles
            tell application "Finder"
                set fileToOpen to POSIX path of (theFile as string)
            end tell
            
            set command to "open -a " & quoted form of edgePath & " " & quoted form of fileToOpen
            do shell script command
        end repeat
    else
        display dialog "Unable to find Microsoft Edge on your system."
    end if
end run

on getLatestEdgePath()
    set edgeVersions to do shell script "mdfind kMDItemCFBundleIdentifier = com.microsoft.edgemac"
    set sortedVersions to paragraphs of (do shell script "echo " & quoted form of edgeVersions & " | sort -rV")
    if (count of sortedVersions) > 0 then
        set latestVersionPath to item 1 of sortedVersions
        set latestEdgePath to POSIX path of latestVersionPath
        return latestEdgePath
    else
        return ""
    end if
end getLatestEdgePath
```
上面这个脚本核心是通过👇的命令找到所有版本的`Edge`然后选择最新版本的`Edge`用于打开PDF文件
```shell
mdfind kMDItemCFBundleIdentifier = com.microsoft.edgemac
```

## 在自动操作中设置
有了上面的准备工作，我们就可以在Mac的`自动操作`中进行设置了，步骤如下：

![image-20230607213231151](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202306072132182.png)

![image-20230607213245347](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202306072132381.png)

![image-20230607213258267](https://gitee.com/zephyrushjnnjh/image-repo/raw/master/img/202306072132294.png)

把Applescript脚本复制过来，保存，然后去键盘快捷键里设置一个快捷键，就大功告成啦！

最后就可以愉快的体验无缝衔接的Zotero $\rightarrow$ Bingchat 体验了，果然懒才是科技进步的动力啊doge!