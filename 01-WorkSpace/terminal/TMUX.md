### Tmux

> 通常情况下，系统环境中是自带 tmux 的，所以不需要手动安装。

- [配置自己的 Tmux](https://github.com/zuorn/tmux.conf)

```shell
# 查看 tmux 版本
tmux -V
```

```shell
# 使用我自己的 tmux.conf 文件
cp config/tmux/.tmux.conf $HOME/.tmux.conf

mkdir -p $HOME/.config/tmux/
cp config/tmux/fzf-panes.tmux $HOME/.config/tmux/fzf-panes.tmux

# 重启 tmux
tmux source-file $HOME/.tmux.conf
```
