---
title: 远程连接ubuntu16.04 
tags: Linux教程
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

<style>table{word-break:initial;}</style>

# 1 图形界面
# 1.1 使用 XDMCP+xmanager+lightdm
第一步：开启XDMCP server
```bash
sudo vim /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf

#添加

[XDMCPserver]
enabled=true
```

第二步：如果启动了ufw，则打开 177端口，


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



# 1.2 使用 mate+xstart
```

sudo apt install ssh mate-desktop-evironment mate-desktop

```

# 1.3 xshell+ssh+xmanager
```
sudo apt install ssh
```
xshell转发x11连接到xmanager

# 使用Xrdp+VNC

```
sudo apt install tightvncserver

echo unity > ~/.xsesstion

sudo apt install xrdp

sudo service xrdp restart

gsetttings set org.gnome.desktop.remote-access.requre-encryption false

```

# 2 终端连接

# 2. 1 xshell+ssh

# 2.2  xstart+ssh




------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------