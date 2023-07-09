# 远程办公



## 远程办公

### SSH客户端
1. Putty

   开源好用，但是不是非常友好

2. Xshell

   由于是商业软件，更新和质量比较稳定且界面友好易用

3. MobaXterm

   功能非常丰富，但是稳定性和易用性没有上面两个优秀

4. Termius

   真·全平台客户端，也比较好，但是感觉用户群体不大
### 虚拟显示器

***因为TeamViewer在没打开显示器的情况下之直接从命令行运行可能存在问题，因此可以通过<font color=red>xorg</font>虚拟显示器来实现给Linux服务器开启TeamViewer***

1. 安装软件(Ubuntu18)：

```shell
$ sudo apt-get install  xserver-xorg-core-hwe-18.04
$ sudo apt-get install  xserver-xorg-video-dummy-hwe-18.04  --fix-missing
```

2. 配置文件：

`/usr/share/X11/xorg.conf.d/dummy-1920x1080.conf`的内容如下（不建议修改）：

```shell
Section "Monitor"
  Identifier "Monitor0"
  HorizSync 28.0-80.0
  VertRefresh 48.0-75.0
  # https://arachnoid.com/modelines/
  # 1920x1080 @ 60.00 Hz (GTF) hsync: 67.08 kHz; pclk: 172.80 MHz
  Modeline "1920x1080_60.00" 172.80 1920 2040 2248 2576 1080 1081 1084 1118 -HSync +Vsync
EndSection

Section "Device"
  Identifier "Card0"
  Driver "dummy"
  VideoRam 256000
EndSection

Section "Screen"
  DefaultDepth 24
  Identifier "Screen0"
  Device "Card0"
  Monitor "Monitor0"
  SubSection "Display"
    Depth 24
    Modes "1920x1080_60.00"
  EndSubSection
EndSection
```

3. 使用方法:

配合nohup

```shell
sudo X -config xorg.conf   				#默认0窗口

sudo X :7 -config dummy-1920x1080.conf 	#开启7窗口

sudo systemctl stop gdm.service 		#关闭xrog
```

```shell
DISPLAY=:0 firefox
DISPLAY=:0 gedit
export DISPLAY=:0
```

4. 参考
[(Ubuntu)无显示器接入，使用虚拟显示器且远程控制](https://segmentfault.com/a/1190000020733143)
[How to run X server using xserver-xorg-video-dummy driver on Ubuntu](https://techoverflow.net/2019/02/23/how-to-run-x-server-using-xserver-xorg-video-dummy-driver-on-ubuntu/)

###  Vscode-终极神器

vscode的Remote-SSH模块十分好用，可以完成远程调试等功能，解决了笔记本运算能力不足的问题，如果遇到一些必须使用显示的情况还是推荐Teamviewer（视频压缩和传输非常好用，但是不建议用来传输文件）
