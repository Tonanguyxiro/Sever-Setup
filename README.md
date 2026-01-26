# Sever-Setup

新服务器开荒教程与脚本。包括环境配置，数据迁移。

## 基础工具 

### Shell

- [安装 zsh 和 oh-my-zsh](https://zhuanlan.zhihu.com/p/476730915)：
```shell
# 手动安装 zsh
wget -O source/zsh.tar.xz https://sourceforge.net/projects/zsh/files/latest/download
mkdir source/zsh && tar -xvf source/zsh.tar.xz -C source/zsh --strip-components 1
cd source/zsh
mkdir -p $HOME/.zsh
./configure --prefix=$HOME/.zsh
make && make install

# 把 zsh 加入到 PATH 中
echo 'export PATH=$HOME/.zsh/bin:$PATH' >> $HOME/.bashrc
source $HOME/.bashrc

# 安装 oh-my-zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 安装 oh-my-zsh 的插件
git clone https://github.com/zsh-users/zsh-autosuggestions $HOME/.zsh/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting $HOME/.zsh/plugins/zsh-syntax-highlighting

# 使用我自己的 zshrc 文件
cp config/zsh/.zshrc $HOME/.zshrc
# 注意：需要把当前用户的 conda 路径替换为你的 conda 路径
```

### Tmux

> 通常情况下，系统环境中是自带 tmux 的，所以不需要手动安装。

- [配置自己的 Tmux](https://github.com/zuorn/tmux.conf)
```shell
# 查看 tmux 版本
tmux -V

# 使用我自己的 tmux.conf 文件
cp config/tmux/.tmux.conf $HOME/.tmux.conf

mkdir -p $HOME/.config/tmux/
cp config/tmux/fzf-panes.tmux $HOME/.config/tmux/fzf-panes.tmux

# 重启 tmux
tmux source-file $HOME/.tmux.conf
```


## 代码环境


### Git & Github

#### Fine-Grained Access Key

```shell
# 查看地址
git remote -v

# 修改地址
git remote set-url origin https://oauth2:<Your-Fine-Grained-Access-Key>@github.com/<Your-Username>/<Your-Repository>.git
```

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

