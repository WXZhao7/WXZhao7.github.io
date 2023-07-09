# SSH的配置



## 前言

在使用远程设备的时候，SSH是一种高效便捷且安全的手段，使用命令行界面可以有效节省资源并减轻带宽压力。远程桌面是一种解决方案，但是除非在局域网环境或者极其奢侈的网络条件下才能有较为稳定低延迟的体验，但是这肯定是对于资源的一种浪费，因此使用SSH或者web界面才是如今的王道！

## 产生密钥

#### ssh-keygen命令

 ```shell
ssh-keygen 
    -b：指定密钥长度；
    -e：读取openssh的私钥或者公钥文件；
    -C：添加注释；
    -f：指定用来保存密钥的文件名；
    -i：读取未加密的ssh-v2兼容的私钥/公钥文件，然后在标准输出设备上显示openssh兼容的私钥/公钥；
    -l：显示公钥文件的指纹数据；
    -N：提供一个新密语；
    -P：提供（旧）密语；
    -q：静默模式；
    -t：指定要创建的密钥类型。
 ```

以上的选项中比较重要的是：`-C, -f, -P`

1. `-C`在github的密钥产生中会用到；
2. `-f`可以指定生成的路径和文件名【 】；
3. `-P`指定密钥的密语（密码）。

#### 举个栗子：

```shell
ssh-keygen -t rsa -b 4096 -C "your_email@email.com" -P '****' -f /path/filename
```

问题1：如果不使用默认名称，需要使用`ssh -i /path/file user@IP ...`来登录

问题2：使用-P或者在交互界面设置密语有助于提高密钥的安全性，但是在设置密语后每次SSH登录均需要密码，为了方便反而带来不便？

这些问题在后面都会解决的！

## 将公钥添加到指定的账户

```shell
#方法1
#一般使用
ssh-copy-id -i /path/file.pub user@IP [-p****]
#有些系统使用
ssh-copy-id -i /path/file.pub "user@IP [-p****]"
#方法2
cat /path/file.pub | ssh [-p **] user@IP "umask 077;mkdir -p ~/.ssh;cat - >> ~/.ssh/authorized_keys"
```
## 删除公钥


> 如果是Linux运维，需要操作大量的设备，使用**ssh端口转发**来解决多设备问题
>
> 参考[ssh端口转发：ssh隧道](http://www.zsythink.net/archives/2450) 和[ssh代理转发](http://www.zsythink.net/archives/2422)

## 使用ssh-agent（ssh代理）管理密钥

ssh并非一定支持ssh-agent转发的连接。要使用ssh-agent的转发功能，需要在sshd_config中开启`AllowAgentForwarding`选项，在ssh_config中开启`ForwardAgent`选项。

#### 1. 启动ssh-agent
在 Linux中 ssh-agent 在 X会话 或 登录会话 之初就已经启动，一般都不会有问题,也可以参考以下办法
```shell
#方式一:创建子shell, 在子shell中运行ssh-agent进程， 退出子shell自动结束代理。
ssh-agent $SHELL
#方式二:单独启动一一个代理进程，退出当前shell时最好使用ssh-agent -k关闭对应代理
eval `ssh-agent`
#GitHub给出的相同方案
eval $(ssh-agent -s)
```
在 Windows 中，我们选取以下方法其中之一即可配置：
* 在 计算机 > 管理 > 服务 > OpenSSH > Authentication Agent> 设置为自动启动
* 管理员PowerShell: `Set-Service ssh-agent -StartupType Automatic`
#### 2. 关闭ssh-agent

```shell
ssh-agent -k
```

#### 3. 添加私钥

```shell
ssh-add /path/file
```

#### 4. 查找和删除密钥

```shell
#查看代理的私钥
ssh-add -I
#查看对应的公钥
ssh-add -L
#删除指定
ssh-add -d /path/file
#删除所有
ssh-add -D
```

#### 5. 锁定和解锁ssh-sgent

```shell
ssh-add -x
ssh-add -X
```
#### 6. ssh-add命令
````shell
usage: ssh-add [options] [file ...]
Options:
  -l          List fingerprints of all identities.
  -E hash     Specify hash algorithm used for fingerprints.
  -L          List public key parameters of all identities.
  -k          Load only keys and not certificates.
  -c          Require confirmation to sign using identities
  -m minleft  Maxsign is only changed if less than minleft are left (for XMSS)
  -M maxsign  Maximum number of signatures allowed (for XMSS)
  -t life     Set lifetime (in seconds) when adding identities.
  -d          Delete identity.
  -D          Delete all identities.
  -x          Lock agent.
  -X          Unlock agent.
  -s pkcs11   Add keys from PKCS#11 provider.
  -e pkcs11   Remove keys provided by PKCS#11 provider.
  -q          Be quiet after a successful operation.
````

## ssh配置文件

更为详细的细节查看：[官方手册说明](https://linux.die.net/man/5/ssh_config)

### ssh客户端按以下优先顺序接收其配置：

1. 从命令行指定的选项
2. 中定义的选项 ~/.ssh/config
3. 中定义的选项 /etc/ssh/ssh_config

### ssh用户配置文件

文件`~/.ssh/config`

常见的配置项

```shell
Host #别名
HostName #主机名
Port #端口
User #用户名
IdentityFile #密钥文件的路径
IdentitiesOnly #只接受SSH key 登录
PreferredAuthentications #强制使用Public Key验证
# 走 HTTP 代理
ProxyCommand socat - PROXY:127.0.0.1:%h:%p,proxyport=8888
# 走 socks5 代理（如 Shadowsocks）
ProxyCommand nc -v -x 127.0.0.1:1088 %h %p
```

比较完整的配置文件

```shell
Host *                       			#选项“Host”只对能够匹配后面字串的计算机有效。“*”表示所有的计算机。
    ForwardAgent no                   	#设置连接是否经过验证代理（如果存在）转发给远程计算机。
    ForwardX11 no                      	#设置X11连接是否被自动重定向到安全的通道和显示集（DISPLAY set）
    RhostsAuthentication no            	#设置是否使用基于rhosts的安全验证。
    RhostsRSAAuthentication no         	#设置是否使用用RSA算法的基于rhosts的安全验证。
    RSAAuthentication yes              	#设置是否使用RSA算法进行安全验证。
    PasswordAuthentication yes         	#设置是否使用口令验证。
    FallBackToRsh no                  	#设置如果用ssh连接出现错误是否自动使用rsh。
    UseRsh no                         	#设置是否在这台计算机上使用“rlogin/rsh”。
    BatchMode no                    	#如果设为“yes”，passphrase/password（交互式输入口令）的提示将被禁止。当不能交互式输入口令的时候,这个选项对脚本文件和批处理任务十分有用。
    CheckHostIP yes               		#设置ssh是否查看连接到服务器的主机的IP地址以防止DNS欺骗。建议设置为“yes”。
    StrictHostKeyChecking no        	#如果设置成“yes”，ssh就不会自动把计算机的密匙加入“$HOME/.ssh/known_hosts”文件，并且一旦计算机的密匙发生了变化， 就拒绝连接。
    IdentityFile ~/.ssh/identity     	#设置从哪个文件读取用户的RSA安全验证标识。
    Port 22                       		#设置连接到远程主机的端口。
    Protocol 2,1　　       				# 选择的 SSH 协议版本，可以是 1 也可以是 2 ，如果要同时支持两者，就必须要使用 2,1 这个分隔了！
    GSSAPIAuthenticationno            	#是否允许使用基于GSSAPI 的用户认证.默认值为"yes".仅用于SSH-2
    Cipher blowfish                    	#设置加密用的密码。
    EscapeChar ~                      	#设置escape字符。
```

#### 跳板机相关设置

* 需要 OpenSSH 7.3 以上版本可以使用`ProxyJump`
举例:
```SHELL
Host tiaoban1    # 代表跳板机 1
    HostName 跳板机 1 的 IP
    Port 22    # ssh 连接端口
    User username1    # 跳板机 1 的用户名

Host tiaoban2    # 代表跳板机 2
    HostName 跳板机 2 的 IP
    Port 22    # ssh 连接端口
    User username2    # 跳板机 2 的用户名

Host target    # 代表目标机器的名字
    HostName 目标机器 IP    # 这个是目标机器的 IP
    Port 22    # 目标机器 ssh 的端口
    User username_target    # 目标机器的用户名
    ProxyJump tiaoban1,tiaoban2

Host 10.10.0.*    # 使用通配符 * 代表 10.10.0.1 - 10.10.0.255
    Port 22    # 服务器端口
    User username    # 服务器用户名
    ProxyJump tiaoban1,tiaoban2
```

* OpenSSH 版本低于 7.3 可以使用`ProxyCommand`
举例:
```SHELL
Host tiaoban   # 任意名字，随便使用
    HostName 跳板机的 IP，支持域名
    Port 22      # 跳板机端口
    User username_tiaoban       # 跳板机用户

Host target      # 同样，任意名字，随便起
    HostName 目标服务器 IP    # 真正登陆的服务器，不支持域名必须IP地址
    Port 22   # 服务器的端口
    User username   # 服务器的用户
    ProxyCommand ssh tiaoban -W %h:%p

Host 10.10.0.*      # 可以用*通配符
    Port 22   # 服务器的端口
    User username   # 服务器的用户
    ProxyCommand ssh tiaoban -W %h:%p
```

### ssh系统配置文件

文件`/etc/ssh/ssh_config`

```shell
#选项参数	                          		说明
Host *	                           	#选项“Host”只对能够匹配后面字串的计算机有效。“*”表示所有的计算机。
ForwardAgent no	                 	#设置连接是否经过验证代理（如果存在）转发给远程计算机。
ForwardX11 no	                  	#设置X11连接是否被自动重定向到安全的通道和显示集（DISPLAY set）
RhostsAuthentication no	          	#设置是否使用基于rhosts的安全验证
RhostsRSAAuthentication no			#设置是否使用用RSA算法的基于rhosts的安全验证
RSAAuthentication yes	           	#设置是否使用RSA算法进行安全验证
PasswordAuthentication yes	      	#设置是否使用口令验证
FallBackToRsh no	              	#设置如果用ssh连接出现错误是否自动使用rsh
UseRsh no	                      	#设置是否在这台计算机上使用“rlogin/rsh”
BatchMode no	                  	#如果设为“yes”，passphrase/password（交互式输入口令）的提示将被禁止。当不能交互式输入口令的时候，这个选项对脚本文件和批处理任务十分有用
CheckHostIP yes	                  	#设置ssh是否查看连接到服务器的主机的IP地址以防止DNS欺骗。建议设置为“yes”
StrictHostKeyChecking no	       	#如果设置成“yes”，ssh就不会自动把计算机的密匙加入“$HOME/.ssh/known_hosts”文件，并且一旦计算机的密匙发生了变化，就拒绝连接
IdentityFile ~/.ssh/identity	   	#设置从哪个文件读取用户的RSA安全验证标识
Port 22	                           	#设置连接到远程主机的端口
Cipher blowfish	                  	#设置加密用的密码
EscapeChar ~	                  	#设置escape字符
```



### ssh服务器配置文件

文件`/etc/ssh/sshd_config`

```shell
#参数选项                  			  说明
Port 22                      		#SSH 预设使用 22 这个 port，您也可以使用多的 port ！
Protocol 2,1                 		#选择的 SSH 协议版本，可以是 1 也可以是 2 ，如果要同时支持两者，就必须要使用 2,1 这个分隔了！
ListenAddress 0.0.0.0          		#监听的主机适配卡！举个例子来说，如果您有两个 IP，分别是 192.168.0.100 及 192.168.2.20 ，那么只想要开放 192.168.0.100 时，就可以写如同下面的样式：
ListenAddress 192.168.0.100      	#只监听来自 192.168.0.100 这个 IP 的SSH联机。如果不使用设定的话，则预设所有接口均接受 SSH
PidFile /var/run/sshd.pid    		#可以放置 SSHD 这个 PID 的档案！左列为默认值
LoginGraceTime 600       			#当使用者连上 SSH server 之后，会出现输入密码的画面，在该画面中，在多久时间内没有成功连上 SSH server ，就断线！时间为秒！
Compression yes          			#是否可以使用压缩指令？
HostKey /etc/ssh/ssh_host_key      	#SSH version 1 使用的私钥
HostKey /etc/ssh/ssh_host_rsa_key  	#SSH version 2 使用的 RSA 私钥
HostKey /etc/ssh/ssh_host_dsa_key 	#SSH version 2 使用的 DSA 私钥
KeyRegenerationInterval 3600    	#由前面联机的说明可以知道， version 1 会使用 server 的 Public Key ，每隔一段时间来重新建立一次！时间为秒！
ServerKeyBits 768          			# Server key 的长度！
SyslogFacility AUTH               	#当有人使用 SSH 登入系统的时候，SSH会记录信息
LogLevel INFO                		#登录记录的等级---》全部
PermitRootLogin no              	#是否允许 root 登入！预设是允许的，但是建议设定成 no！
UserLogin no                     	#在 SSH 底下本来就不接受 login 这个程序的登入！
StrictModes yes                   	#当使用者的 host key 改变之后，Server 就不接受联机
RSAAuthentication yes           	#是否使用纯的 RSA 认证！？仅针对 version 1 ！
PubkeyAuthentication yes        	#是否允许 Public Key ？只有 version 2
AuthorizedKeysFile   .ssh/authorized_keys    	#设定若要使用不需要密码登入的账号时，那么那个账号的存放档案所在档名！
RhostsAuthentication no         	#本机系统不使用 .rhosts ， .rhosts 不安全！
IgnoreRhosts yes            		#是否取消使用 ~/.ssh/.rhosts 来做为认证！
RhostsRSAAuthentication no     		#针对 version 1 ，使用 rhosts 档案在/etc/hosts.equiv配合 RSA 演算方式来进行认证！
HostbasedAuthentication no     		#这个项目与上面的项目类似，不过是给 version 2 使用的！
IgnoreUserKnownHosts no        		#是否忽略家目录内的 ~/.ssh/known_hosts 这个档案所记录的主机内容
PasswordAuthentication yes      	#密码验证当然是需要的！
PermitEmptyPasswords no          	#上面那一项如果设定为 yes 的话，这一项就最好设定为 no ，这个项目在是否允许以空的密码登入！
ChallengeResponseAuthentication yes  	#挑战任何的密码认证！所以，任何 login.conf 规定的认证方式，均可适用！
PAMAuthenticationViaKbdInt yes    	#是否启用其它的 PAM 模块！启用这个模块将会导致 PasswordAuthentication 设定失效！

#与Kerberos 有关的参数设定！底下不用设定
KerberosAuthentication no
KerberosOrLocalPasswd yes
KerberosTicketCleanup yes
KerberosTgtPassing no

#有关在 X-Window 底下使用的相关设定
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes

PrintMotd no                    	#登入后是否显示出一些信息呢？例如上次登入的时间、地点等，预设是 yes ，但是，如果为了安全，可以考虑改为 no ！
PrintLastLog yes              		#显示上次登入的信息！预设也是 yes 
KeepAlive yes                    	#一般而言，如果设定这项目的话，那么 SSH Server 会传送KeepAlive 的讯息给 Client 端，以确保两者的联机正常！在这个情况下，任何一端死掉后， SSH 可以立刻知道！而不会有僵尸程序的发生！
UsePrivilegeSeparation yes       	#使用者的权限设定项目！
MaxStartups 10                 		#同时允许几个尚未登入的联机画面
DenyUsers *                  		#设定受抵挡的使用者名称
AllowUsers *                    	#设定允许的使用者名称
```

## 个人使用示例

#### 配置示例

```shell
# ~/.ssh/config
Host Work
    HostName 39.***.***.80
    User ***
    Port ***
    PreferredAuthentications publickey 
    IdentityFile C:\Users\***\.ssh\id_rsa_work
    AddkeysToAgent yes
Host github
    HostName github.com 
    User git
    PreferredAuthentications publickey 
    IdentityFile C:\Users\***\.ssh\id_rsa_github_PC
    AddkeysToAgent yes
    ProxyCommand connect -S 127.0.0.1:1080 %h %p
```
#### 实际使用栗子

```shell
eval $(ssh-agent -s)
#github
git clone github:XuzhaoWang/test.git
ssh -T github
#other
ssh Work
```

## 参考文章

0. [ssh命令和SSH服务详解](https://www.cnblogs.com/f-ck-need-u/p/7129122.html)

1. [SSH概述与配置文件说明](https://www.cnblogs.com/pipci/p/9791707.html)

2. [SSH Config 那些你所知道和不知道的事](https://deepzz.com/post/how-to-setup-ssh-config.html)

3. [ssh转发代理：ssh-agent用法详解](https://www.cnblogs.com/f-ck-need-u/p/10484531.html)

4. [ssh转发代理：ssh-agent用法详解](https://www.cnblogs.com/f-ck-need-u/p/10484531.html)

5. [SSH隧道：端口转发功能详解](https://www.cnblogs.com/f-ck-need-u/p/10482832.html)
