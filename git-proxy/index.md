# git使用代理加速



## 0.写在前面

Git 目前支持的四种协议本地协议、 `git://`、`ssh://` 和 `http://`，其代理配置各不相同：`core.gitproxy` 用于 `git://` 协议（没用过，不管它），`http.proxy` 用于 `http://` 协议，`ssh://` 协议的代理需要配置 ssh 的 `ProxyCommand` 参数。

### 代理问题

**如何使用不存在的Google不在讨论范围内！但是使用全局代理也并不能对git有效，原理不做讨论~**

- 如果远程仓库的格式像下面那样，这种就是**使用 HTTP 或 HTTPS 协议连接到 Git 仓库**的情况

```
http://github.com/cms-sw/cmssw.git
https://github.com/cms-sw/cmssw.git
```

- 如果远程仓库的格式像下面那样，这种就是**使用 SSH 协议连接到 Git 仓库**的情况

```
git@github.com:cms-sw/cmssw.git
ssh://git@github.com/cms-sw/cmssw.git
```

## 1. HTTP形式

### 1.1 HTTP代理的配置

针对所有域名的 Git 仓库

```shell
git config –global http.proxy protocol://127.0.0.1:port
```
**注意**： --glboal 选项指的是修改 Git 的全局配置文件 ~/.gitconfig，而非各个 Git 仓库里的配置文件 .git/config。protocol 指的是代理的协议，如 http，https，socks5 等。port 则为端口号。
**举例**：

```shell
#socks5协议
git config --global http.proxy socks5://127.0.0.1:1088
#http协议
git config --global http.proxy http://127.0.0.1:8888
#https协议
git config --global http.proxy https://127.0.0.1:8888
```

针对特定域名的 Git 仓库

```shell
git config –global http.url.proxy protocol://127.0.0.1:port
```
**注意**:

- 此处的 url 即为你需要走代理的仓库域名，url 以 http:// 和 https:// 打头的均用这个方法。
- 网上很多中文教程，可能会告诉你 https:// 打头的 url 使用“git config –global https.https://example.com.proxy protocol://127.0.0.1:port”，这种做法其实是错的！记住一点：Git 不认 https.proxy*，设置 *http.proxy 就可以支持 https 了。
- 如果想了解 url 的更多模式，如子域名等的情况，可参照 [Git 的官方文档](https://git-scm.com/docs/git-config) 。网页内容搜索 http.<url>.*，即可找到相关信息。

**举例**: 

```
git clone https://github.com/<user>/<repository>.git
```

```shell
# socks5协议
git config --global http.https://github.com.proxy socks5://127.0.0.1:1088
# http协议
git config --global http.https://github.com.proxy http://127.0.0.1:8888
```

### 1.2 HTTP代理的使用

```shell
# 查看所有配置
git config -l
# reset 代理设置
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 2. SSH形式

在这种情况下，Git 依靠 ssh 处理连接； 为了通过代理进行连接，您必须配置 ssh 本身，在 `~/.ssh/config` 文件中设置 **ProxyCommand** 选项。Linux 和 macOS 是通过 `nc` 来执行 ProxyCommand 的，Windows 下则是通过 `connect`。

Windows更多的细节参考[connect官方文档](https://bitbucket.org/gotoh/connect/wiki/Home#!more-detail)

Linux & Mac直接使用`netcat`完成相关操作
### Windows
```shell
ProxyCommand "C:\Program Files\Git\mingw64\bin\connect.exe" -S 127.0.0.1:1088 %h %p
ProxyCommand "C:\Program Files\Git\mingw64\bin\connect.exe" -H 127.0.0.1:8888 %h %p
# 测试省略路径依旧可以实现
# SOCKS
ProxyCommand connect -S 127.0.0.1:1080 %h %p
# HTTP
ProxyCommand connect -H 127.0.0.1:1080 %h %p
```
### Linux & Mac
```shell
# 完整语句
ProxyCommand /usr/bin/ncat --proxy 127.0.0.1:1088 --proxy-type http %h %p
# HTTP
ProxyCommand nc -X connect -x 127.0.0.1:7890 %h %p
# SOCKS
ProxyCommand nc -X 5 -x 127.0.0.1:7891 %h %p
ProxyCommand nc -x 127.0.0.1:7891 %h %p
```
所以以下 2 种的写法效果一样 ，都指的是走 socks5 代理：

>  * 如果你的代理使用的协议是 socks4/5 , 修改 --proxy-type 后面的协议即可, 例如
>    --proxy-type socks4
>    --proxy-type socks5
>  * 在调用 ProxyCommand 时，％h 和 ％p 将会被自动替换为目标主机名和 SSH 命令指定的端口（%h 和 %p 不要修改，保留原样即可）。
>  * -X 选项后面接的是 connect 的意思是 HTTPS 代理。
>    -x 选项后面加上代理地址和端口号。
>  * 如果 -X 选项后面接的是数字 5，那么指的就是 socks5 代理。
>    当然你直接不写上 -X 选项也是可以的，因为在没有指定协议的情况下，默认是使用socks5代理的。

## 引用

### 主要参考文章

[一文让你了解如何为 Git 设置代理](https://ericclose.github.io/git-proxy-config.html)

### 官方文档

[ssh_config](https://man.openbsd.org/ssh_config) ProxyCommand 的内容

[nc](https://man.openbsd.org/nc) `-X` 和 `-x` 选项的的内容:

[connect](https://bitbucket.org/gotoh/connect/wiki/Home#!more-detail) `-H` 和 `S` 选项的内容
