---
date created: 2023-02-24 14:43:14 +08:00
date updated: 2023-02-27 01:47:23 +08:00
title: Launchd
share: true
category: Productivity/Mac
---

# Launchd是什么

[Wikipedia](http://en.wikipedia.org/wiki/Launchd) defines launchd as "a unified, open-source service management framework for starting, stopping and managing daemons, applications, processes, and scripts. Written and designed by Dave Zarzycki at Apple, it was introduced with Mac OS X Tiger and is licensed under the Apache License."

一句话概括：**Launchd是MacOS上用来管理后台任务的框架。**

被管理的后台任务有两种类型：Daemon和Agent。
- Daemon是系统或者管理员定义的维护性程序，在系统启动以后加载
- Agent是系统/管理员/用户定义的在登陆后才加载的程序

按服务类型和配置文件存储路径可进一步划分:

| Type           | Location                      | Run on behalf of         |
| -------------- | ----------------------------- | ------------------------ |
| User Agents    | ~/Library/LaunchAgents        | Currently logged in user |
| Global Agents  | /Library/LaunchAgents         | Currently logged in user |
| System Agents  | /System/Library/LaunchAgents  | Currently logged in user |
| Global Daemons | /Library/LaunchDaemons        | root                     |
| System Daemons | /System/Library/LaunchDaemons | root                     |

# 任务(Job)定义

在上面列表中的路径下存储了各个plist文件，它们控制着Daemon/Agent行为。plist文件采用xml格式。根据存储名称的不同，很容易区分是Daemon还是Agent。例如将其放置到~/Library/LaunchAgents目录下：那么这明显是一个Agent任务，在用户登陆后自动加载这个plist中配置的程序，并在适当的时候触发执行。

**一个最简单的任务需定义3个字段**

- Label设置服务名称
- Program设置服务运行每次要执行的命令
- RunAtLoad设置服务在加载后立刻执行

```
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"> 
<plist version="1.0"> 
	<dict> 
		<key>Label</key> 
		<string>com.example.app</string> 
		
		<key>Program</key>
		<string>/Users/Me/Scripts/cleanup.sh</string> 
		
		<key>RunAtLoad</key> 
		<true/> 
	</dict> 
</plist>
```

**plist中的配置很丰富**

这里只列出部分常用的，完整的配置强力推荐参考：[launchd.info | launchd的完全参考手册](https://www.launchd.info/)

| Key                   | 属性类别      | 说明                                   |
| --------------------- | ------------- | -------------------------------------- |
| Label                 | Job name      | 任务名                                 |
| Program               | What to Start | 命令的字符串                           |
| ProgramArguments      | What to Start | 命令的字符串数组                       |
| EnvironmentVariables  | Environment   | 设置环境变量，不支持globbing和变量替换 |
| StandardInPath        | Environment   | 标准输入路径                           |
| StandardOutPath       | Environment   | 标准输出路径                           |
| StandardErrorPath     | Environment   | 标准错误输出路径                       |
| WorkingDirectory      | Environment   | 工作目录,默认是/                       |
| SoftSourceLimit       | Job Constrain | 使用资源的软顶                         |
| HardSourceLimit       | Job Constrain | 使用资源的硬顶                         |
| RunAtLoad             | When to Start | Load后启动                             |
| StartInterval         | When to Start | 间隔一段时间后执行                     |
| StartCalendarInterval | When to Start | 固定的日期时间执行                     |
| StartOnMount          | When to Start | 设备挂载后执行                         |
| WatchPaths            | When to Start | 路径变更后执行                         |
| ...                   | ...           | ...                                    |

# 任务管理命令

我们已经知道Daemons在系统启动后加载，而Agent在用户登陆后加载。其实还可以使用`launchctl`以手动方式对任务进行操作。

**命令行工具**:launchctl

- 加载(Load)任务
```
launchctl load ~/Library/LaunchAgents/com.example.app.plist
```
- 卸载(Unload)任务
```
launchctl unload ~/Library/LaunchAgents/com.example.app.plist
```
- 启动(Start)任务
```
launchctl start com.example.app
```
- 查看任务的状态
```
launchctl list|grep com.example.app
```

**Load和Start的区别**

对任务进行Load操作，不代表执行这个任务中的命令。这是因为任务可以配置不同的启动执行场景，比如：网卡就绪后执行、文件夹中有变更时执行、固定时间18:00执行、间隔1min执行一次，等等，非常灵活。

调用`launchctl start`会手动触发任务中的命令（不管它配置的启动条件是什么）

**可视化的工具**

另外可以使用可视化的工具LaunchControl以方便对任务进行调试，试用模式可以查看任务、日志、报错，但是不能保存修改。只有付费购买后才能保存新建/修改的任务，可酌情购买，虽然确实能大大节省调试的时间，但个人License售价$21并不便宜，使用适用模式即可。

```
brew install launchcontrol
```

![Pasted image 20230224140720.png](../../img/Pasted%20image%2020230224140720.png)

# 常见问题

1. 执行权限问题。plist文件中配置的Program需要有可执行权限，可通过如下命令添加

```
chmod +x /Users/Me/Scripts/cleanup.sh
```

2. 磁盘权限问题：如果Program是.sh文件，那么`sh`命令本身还需要具有Full Disk Access权限。
在Privacy&Security -> Full Disk Access中打开sh的权限。如果用bash/zsh则替换为对bash/zsh进行Full Disk Access授权。

![2023-02-24_14.39.46.png](../../img/2023-02-24_14.39.46.png)

# 参考工具

1. [launchd.info | launchd的完全参考手册](https://www.launchd.info/)
2. [LaunchControl](https://www.soma-zone.com/LaunchControl/)
