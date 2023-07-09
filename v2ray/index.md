# v2ray简明使用方法


## 前言摘要

前情提要: 个人简单写了一些基础的东西, 没有追求准确性or完备性. [有问题请想方设法百度10分钟]

制作目的: 使用**谷歌系列和维基百科**帮助<u>科学研究和日常学习</u>, **<u>禁止用于其他目的</u>**, 违者必究.

制作背景: <u>谷歌上网助手已经不能访问谷歌</u>了, 但对于科研学习而言谷歌和维基百科的价值依旧很高.

制作者:	WXZhao

## 简单科普

* 出于国家政策考量, 为了保护大多数公民的信息安全, 大陆地区在GFW的保护下不能访问部分国外网站. 政策的出发点是好的, 但是这却是对于部分人群产生了一定的影响, 因此我们需要通过一些特殊渠道才能访问有用的国外网站(如谷歌学术, 维基百科等)
* 方法总结: **流量代理**, 简单来说就是虽然是大陆本地设备进行访问, 但是这部分的流量被转发到**可以访问国外网站的设备**上, 再将结果实时返还到本地电脑上. (这里"可以访问国外网站的设备"简单来说就是一台位于国外的计算机)
* 直接转发的流量会被GFW识别并拦截, 所以在实际使用时会使用VPN/SS/SSR/v2ray/trojan等手段对流量进行加密包装, 尽管如此还是会在一定程度上受到影响, 偶尔出现无法访问的情况.

## 重要概念

* 系统代理: 让本机的所有流量经过代理软件
* 直接连接: 让本机的所有流量正常进行访问
* PAC/规则/高级路由设置: 所有流量走服务器很亏(延时/速度/金钱), 所以通过这些规则让不能访问的流量通过服务器进行访问, 其余的正常访问

## 服务器

* 有能力或者有更高需求的同学建议直接购买VPS, 自行搭建v2ray/trojan服务, SS/SSR不再推荐使用.

* 普通用户推荐直接购买成品机场, 机场一般线路较多, 不需要自己维护.


> 机场的获取方法个人想办法, 本文不负责提供找机场比价担保稳定性等.
>
> 这里用一个我最近在用的机场作为示例, 简单展示下如何进行购买和使用, 

官方网址(国内无法直接访问): https://paoluz.pw/

个人推广链接(会自动跳国内):https://owo-qvq-uvu-owo.xn--mes358a082apda.com/auth/register?code=Cyhw

### 1. 注册账户

<u>**个人邀请码: Cyhw**</u>

![注册界面](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-06-12T18:21:50.657-image-20210612182145063.png)

### 2. 购买套餐 

**购买之前需要充值, 合理选择适合自己的套餐**

![购买套餐](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-06-12T18:32:43.371-image-20210612183238896.png)

### 3. 找到订阅链接 

> 如果想要自学直接使用机场内的"快速使用/社区教程"

![订阅中心](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-06-12T18:37:02.929-image-20210612183659173.png)

## PC端的使用

* **请务必保证本机时间与标准时间相差不超过1s, 否则会无法正常使用!!!**
* **测试使用的浏览器建议为新安装的浏览器, 以防止个人操作带来的干扰**

> 本文仅使用全PC平台的Qv2ray做演示:
>
> qv2ray最新下载网址: https://github.com/Qv2ray/Qv2ray/releases/latest
> 蓝奏云下载: https://wwr.lanzoui.com/b02c5kted
> 密码:v2ray
> v2ray-core最新下载网址: https://github.com/v2fly/v2ray-core/releases/latest
> 蓝奏云下载: https://wwr.lanzoui.com/b02c5ktba
> 密码:v2ray

### 1. 安装软件

* windows安装exe文件即可
* linux可以直接使用AppImage即可[需要先`chmod +x ***.AppImage`], 双击/命令行运行

* mac安装对应dmg文件

### 2. 安装内核

* 下载对应平台的v2ray-core并解压
* 首选项 - 内核设置 - 设置内核文件位置 - 检查V2Ray核心设置, 选择自己的解压目录/在默认目录建立文件夹再将文件移动到路径

![内核设置](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-06-12T18:49:37.413-image-20210612184931689.png)

* 内核设置成功的提示

![内核设置成功](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-06-12T18:58:05.911-image-20210612185259375.png)

### 3. 订阅设置
* 在分组中创建分组, 使用之前的复制的订阅链接置于订阅地址, 更新订阅并保存

![image-20210612185856941](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-06-12T19:01:05.459-image-20210612185856941.png)

### 4. 使用订阅的某条线路 

> 自行测试哪一条更好用, 可以通过右键测试延迟确定哪些可用

![image-20210612190059723](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-06-12T19:01:12.409-image-20210612190059723.png)

### 5. 实际使用测试 [需要先启用系统代理, 以保证使用]
<img src="https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-06-12T19:04:12.965-image-20210612190329060.png" alt="image-20210612190329060" style="zoom:50%;" />

​	![image-20210613001709395](../../AppData/Roaming/Typora/typora-user-images/image-20210613001709395.png)

### 6. 其他问题解决:

* 如果chrome无法使用, 先用EDGE浏览器or火狐浏览器进行测试, chrome问题可能是由于VPN类插件导致的, 也可能是其他的问题.
* chrome如何都不能使用时建议重新卸载安装or使用[Proxy SwitchyOmega ](https://proxy-switchyomega.com/)插件解决问题,官网有详细教程

## 手机端

懒得写了, 参考一般机场中会提供的教程,比电脑端更加简单![](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-06-12T19:12:01.286-image-20210612191134074.png)
