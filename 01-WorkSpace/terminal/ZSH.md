# [安装 zsh 和 oh-my-zsh](https://zhuanlan.zhihu.com/p/476730915)：

## 安装 ZSH
直接安装 zsh


手动安装 zsh
```shell
wget -O source/zsh.tar.xz https://sourceforge.net/projects/zsh/files/latest/download
mkdir source/zsh && tar -xvf source/zsh.tar.xz -C source/zsh --strip-components 1
cd source/zsh
mkdir -p $HOME/.zsh
./configure --prefix=$HOME/.zsh
make && make install

# 把 zsh 加入到 PATH 中
echo 'export PATH=$HOME/.zsh/bin:$PATH' >> $HOME/.bashrc
source $HOME/.bashrc
```


# 安装 oh-my-zsh
```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

# 安装 oh-my-zsh 的插件
git clone https://github.com/zsh-users/zsh-autosuggestions $HOME/.zsh/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting $HOME/.zsh/plugins/zsh-syntax-highlighting

# 使用我自己的 zshrc 文件
cp config/zsh/.zshrc $HOME/.zshrc
# 注意：需要把当前用户的 conda 路径替换为你的 conda 路径
