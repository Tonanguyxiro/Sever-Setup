# Setup Anything

新服务器开荒教程与脚本。包括环境配置，数据迁移。







## 使用说明

## 基础配置

### 换源

```shell
sudo cp -a /etc/apt/sources.list /etc/apt/sources.list.bak
```

Edit Configuration

```shell
sudo sed -i "s@http://.*archive.ubuntu.com@<https://mirrors.sustech.edu.cn@g>" /etc/apt/sources.list
sudo sed -i "s@http://.*security.ubuntu.com@<https://mirrors.sustech.edu.cn@g>" /etc/apt/sources.list
```

Refresh Repository Indexes

```shell
sudo apt-get update
```

