---
title: 远程桌面ubuntu16.04 
tags: Linux教程
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

<style>table{word-break:initial;}</style>

# 1 使用 XDMCP+xmanager
第一步：开启XDMCP server
```bash
sudo vim /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf

#添加

[XDMCPserver]
enabled=true
```

第二步：打开 177端口
```
sudo ufw allow 177
```

第三步：安装xubuntu
```
sudo apt install xubuntu-desktop
```
第四步：重启lightdm
```
sudo service lightdm restart
```

# 使用Xrdp+VNC



------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------