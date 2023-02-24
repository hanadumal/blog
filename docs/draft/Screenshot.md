---
date created: 2023-02-23 17:56:39 +08:00
date updated: 2023-02-24 21:04:19 +08:00
share: true
---

修改截图名称
```bash
# default is 1, in case of you had change it, set "include-date" 1 
defaults write com.apple.screencapture "include-date" 1 
# remove prefix from screenshot name 
defaults write com.apple.screencapture name "ScreenShot"
```

自动修改截图格式

```bash
#!/bin/bash

mv ~/Desktop/ScreenShot*.png ~/Desktop/"`date "+%Y-%m-%d-%H.%M.%S"`.png"; $2>/dev/null
```

```bash
chmod +x /usr/local/bin/screencap_rename.sh
```

~/Library/LaunchAgents/usr.screenshot.rename.plist
```bash
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>usr.screenshot.rename</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/usr/local/bin/screencap_rename.sh</string>
    </array>
    <key>WatchPaths</key>
    <array>
        <string>/Users/wangsheng/Desktop</string>
    </array>
</dict>
</plist>
```


```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>usr.screenshot.rename</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/usr/local/bin/screencap_rename.sh</string>
    </array>
    <key>StandardOutPath</key>
    <string>/tmp/usr_screen_rename.out</string>
    <key>StandardErrorPath</key>
    <string>/tmp/usr_screen_rename.err</string>
    <key>WatchPaths</key>
    <array>
        <string>/Users/wangsheng/Desktop</string>
    </array>
</dict>
</plist>
```


```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>work.hanadumal.test</string>
    
    <key>Program</key>
    <string>/Users/wangsheng/hello.sh</string>
    
    <key>StandardErrorPath</key>
    <string>/Users/wangsheng/ws.log</string>
    
    <key>StandardOutPath</key>
    <string>/Users/wangsheng/ws.log</string>
</dict>
</plist>
```

launchctl调用的脚本的cwd路径是'/'(已经验证)

bash中如何处理带空格的文件名。使用`IFS（the Internal Field Separator）`，Shell依靠它去决定如何进行单词分隔。先把IFS换成\n,在脚本执行完后，再重置它。

```bash
#!/bin/bash
SAVEIFS=$IFS
IFS=$'\n'
for f in `ls Screen*.png`
do
    echo "$f"
done
IFS=$SAVEIFS
```

假设$HOME下有几张截图，这个时候，我们的测试脚本变为如下，将会输出带空格的截图文件名到hello.txt中
```bash
#!/bin/bash
SAVEIFS=$IFS
IFS=$'\n'

for filename in `ls $HOME/ScreenShot*.png`
do
    echo ${filename} >> /Users/wangsheng/hello.txt
done
echo "hello" >> /Users/wangsheng/hello.txt
IFS=$SAVEIFS
```

尝试切换路径到$HOME/Desktop/ScreenShot*.png
```bash
#!/bin/bash
SAVEIFS=$IFS
IFS=$'\n'
for filename in `ls $HOME/Desktop/ScreenShot*.png`
do
    echo ${filename}# >> /Users/wangsheng/hello.txt
done
echo "hello" >> /Users/wangsheng/hello.txt
IFS=$SAVEIFS
```
仍然会报错找不到路径
```
ls: /Users/wangsheng/Desktop/ScreenShot*.png: No such file or directory
```
![ScreenShot 2023-02-23 at 21.06.36.png](../img/ScreenShot%202023-02-23%20at%2021.06.36.png)
怀疑是不是没有赋予全部访问权限的问题。确认了在把脚本和plist都修改成sh，并且赋给sh FULL Disk Access以后，问题解决。

又顺带学习shell中字符处理的方法。https://blog.csdn.net/dongwuming/article/details/50605911

### 最终版本

```bash
#!/bin/sh

SAVEIFS=$IFS
IFS=$'\n'

for file_path in `ls $HOME/Desktop/ScreenShot*.png`
do
	old_path=${file_path}
    new_path=${file_path/ScreenShot /}
    new_path=${new_path/ at /_}
    mv ${old_path} ${new_path}
done

IFS=$SAVEIFS
```


```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>hana.screenshot.rename</string>

    <key>Program</key>
    <string>/Users/wangsheng/hello.sh</string>

    <key>StandardErrorPath</key>
    <string>/Users/wangsheng/ws.log</string>

    <key>StandardOutPath</key>
    <string>/Users/wangsheng/ws.log</string>

    <key>WatchPaths</key>
    <array>
        <string>/Users/wangsheng/Desktop</string>
    </array>
</dict>
</plist>
```

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>hana.screenshot.rename</string>
    
    <key>Program</key>
	<string>/usr/local/bin/screencap_rename.sh</string>
    
    <key>StandardOutPath</key>
    <string>/tmp/hana_screen_rename.out</string>
    <key>StandardErrorPath</key>
    <string>/tmp/hana_screen_rename.err</string>
    
    <key>WatchPaths</key>
    <array>
        <string>/Users/wangsheng/Desktop</string>
    </array>
</dict>
</plist>
```


load the plist
```bash
launchctl load ~/Library/LaunchAgents/usr.screenshot.rename.plist
```

参考文献中使用的ProgramArguments,而不是Program，在我验证的过程中报错？原因是什么？

后面的讨论中也指出了，这种方法有两个问题：
1. 不能有重复文件
2. 需要对/bin/bash赋予Full Disk Access(为啥我看完没想到是这个问题，不过好在是把launchctl的东西完整学了一遍。)

疑问：
FULL ACCESS想添加bash，从哪里找到bash呢？Macintoch？
![ScreenShot 2023-02-23 at 21.47.06.png](../img/ScreenShot%202023-02-23%20at%2021.47.06.png)
[1]  [how to change the format of osx screenshot](https://apple.stackexchange.com/questions/251385/how-do-you-change-the-format-of-the-osx-screen-shot-file-name)

todo: 整合成一键脚本


## 多媒体文件转换
### mov格式转换gif
在用录屏生成视频文件的大小通常较大，为了方便上传发布，可以在终端里命令行的方式来做.mov文件到.gif的转换。
```
brew install gifify
```

![ 2023-02-23 at 18.20.08.png](2023-02-23%20at%2018.20.08.png)
依赖项很多，下载和安装的时间较长，耐心等待完成查看使用手册：
```
gifify -h

```
转换命令如下:
```
gifify timer.mov -o timer.gif
```
