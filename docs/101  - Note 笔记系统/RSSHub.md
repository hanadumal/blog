---
date created: 2023-02-20 17:35:30 +08:00
date updated: 2023-02-21 13:43:09 +08:00
share: true
---
## 部署属于你自己的RSSHub
### 1.手动安装过程
```bash
git clone https://github.com/DIYgod/RSSHub.git
cd RSSHub
# npm ci --production 在我的环境报错，有兴趣可以深入解决
npm install 
```
更详细可参考 [RSSHub官方部署文档](https://docs.rsshub.app/install/#shou-dong-bu-shu-an-zhuang)

### 2.额外但必须的配置
在RSSHub的根目录下，创建.env配置文件。
我主要添加了Youtube、Twitter的配置。另外因为仅供自己使用，添加访问控制的AccessKey。
![[2023-02-20_at_17_39_43.png]]

这样当访问中不带accessKey，会Access Denied，如下所示：

![[2023-02-20_at_17_59_09.png]]
### 3.手动从后台启动
```
pm2 start lib/index.js --name rsshub
```
### 4.systemd开机启动
```
vim /etc/systemd/system/rsshub.service
```

rsshub.service中的内容如下：

```
[Unit]  
Description=RSSHub  
After=network.target  
  
[Service]  
User=vincent  
Group=vincent  
Type=forking  
WorkingDirectory=/opt/RSSHub  
ExecStart=/usr/local/bin/start_rsshub.sh  
  
[Install]  
WantedBy=multi-user.target
```

因systemd只能使用特定的(参考systemd的信息)目录下的二进制文件，需要编写sh脚本如下,并cp到/usr/local/bin/下，注意`#!/bin/sh`不能省略，否则systemctl start会报文件格式错误。
```
#！/bin/sh  
cd /opt/RSSHub && /opt/node-v14.17.4-linux-x64/bin/pm2 start /opt/RSSHub/lib/index.js --name rsshub
```

之后就可以启动服务，并添加开机启动项目了：
```
systemctl daemon-reload
systemctl start rsshub
systemctl status rsshub

systemctl enable rsshub
```
### 5.添加自动更新
```
# pull RSSHub At 04:00 on Sunday  
0 4 * * 0  cd /opt/RSSHub && /usr/bin/git pull >> /opt/logs/pull_rsshub.log 2>&1
```