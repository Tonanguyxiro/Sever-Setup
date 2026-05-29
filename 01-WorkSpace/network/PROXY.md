# 为内网服务器配置代理

前提：假设服务器的IP地址A.B.C.D，用户名为cs，当自己接入了学校/公司内网后，可以通过ssh连接到服务器上：

```bash
ssh cs@A.B.C.D
```

为了能够让服务器连上互联网，我们需要在自己的电脑上安装clash，并且将 设置为系统代码 打开。注意这里的混合代理端口被设为默认的7890。(只需要安装并启动这款软件即可，并不需要导入节点啥的)


## 直接使用 ssh 反向代理

接着，在本机上重启一个终端（后续的所有操作都需要在这个终端上进行，包括使用pip命令），执行如下指令：

```bash
ssh -R A.B.C.D:7891:127.0.0.1:7890 cs@A.B.C.D
```
该指令本质上是ssh cs@A.B.C.D，即会连接到服务器上。前面的配置参数的含义是：将服务器上的7891端口的流量转发到自己电脑的7890端口上，其中的7890需要和自己的clash中的混合代理端口相同。

然后在这个终端上执行如下命令，该命令将服务器上的https, http的流量转发到7891端口。

```bash
export https_proxy=http://127.0.0.1:7891 http_proxy=http://127.0.0.1:7891 all_proxy=socks5://127.0.0.1:7891
```
此时在服务器上就可以使用`pip install xxx`来安装网络上的Python包了。如果你想要使用conda install来安装Python包，在终端执行如下内容后，就可以使用conda install命令了。

```bash
conda config --set proxy_servers.http http://127.0.0.1:7891
conda config --set proxy_servers.https https://127.0.0.1:7891
```

## 在 github ssh 上配置反向代理

## 高级配置

## 无网设置（通用）

```shell
# 使用带代理的 ssh 链接服务器，并转发端口到本地
ssh -R <server-ip>:20121:127.0.0.1:20122 <user-name>@<server-ip>

# 在服务器端设置代理
export https_proxy=http://127.0.0.1:20121 http_proxy=http://127.0.0.1:20121 all_proxy=socks5://127.0.0.1:20121
```

```config
Host 
```
