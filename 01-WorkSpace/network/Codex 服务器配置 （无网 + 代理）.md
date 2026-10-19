
> [!NOTE] Reference 
> [在受限网络环境下使用 VS Code Remote-SSH + 本地代理 + Codex 插件完整教程](https://zhuanlan.zhihu.com/p/2009590635548665051)

# 配置代理链路

> [!NOTE] 查看、测试当前监听端口
> ```powershell
> netstat -ano | findstr LISTENING
> netstat -ano | findstr 20122
> // 应该可以看到
> TCP  127.0.0.1:20122  LISTENING
> ```

- **本地代理端口** 20122
- 在本地Clash中打开`允许局域网连接入`

## 配置 隧道

### VS Code

- `C:\Users\<用户名>\.ssh\config`

```
Host my-remote-server
  HostName <你的服务器IP或域名>
  Port <SSH端口>
  User <用户名>

  # 让服务器通过本地代理出网
  RemoteForward 20122:127.0.0.1:20122

  # 浏览器 OAuth 回调端口
  LocalForward 1455:127.0.0.1:1455

  ServerAliveInterval 60
  ServerAliveCountMax 3
```


### SSH 

`ssh [-N] -R 20122:127.0.0.1:20122 -L 1455:127.0.0.1:1455 my-remote-server`


> [!tip] ssh tips
> - `-L`：把远程的服务拉到本地用（Local Forward）
> - `-R`：把本地的服务暴露给远程用（Reverse Forward）
> - `-N`：不执行远程命令，**只做隧道**


## 服务器端配置代理

### 系统端

在 `~/.bashrc` 中配置

```bash
# ===== Auto proxy config for local forwarded proxy ===== BEGIN

export PROXY_HOST_A6000="u8e5741:5JLxifpc441zIZw1kNayC3L4@127.0.0.1"
export PROXY_HOST="127.0.0.1"
export PROXY_PORT="20122"
export PROXY_URL="http://${PROXY_HOST}:${PROXY_PORT}"

_port_listening() {
    if command -v ss >/dev/null 2>&1; then
        ss -ltn "( sport = :${PROXY_PORT} )" 2>/dev/null | grep -q ":${PROXY_PORT}"
    elif command -v lsof >/dev/null 2>&1; then
        lsof -iTCP:${PROXY_PORT} -sTCP:LISTEN >/dev/null 2>&1
    else
        # fallback: only checks whether connection succeeds
        timeout 1 bash -c ":</dev/tcp/${PROXY_HOST}/${PROXY_PORT}" >/dev/null 2>&1
    fi
}

enable_proxy() {
    export HTTP_PROXY="${PROXY_URL}"
    export HTTPS_PROXY="${PROXY_URL}"
    export ALL_PROXY="${PROXY_URL}"

    export http_proxy="${PROXY_URL}"
    export https_proxy="${PROXY_URL}"
    export all_proxy="${PROXY_URL}"

    export NO_PROXY="localhost,127.0.0.1,::1"
    export no_proxy="${NO_PROXY}"
}

disable_proxy() {
    unset HTTP_PROXY HTTPS_PROXY ALL_PROXY
    unset http_proxy https_proxy all_proxy
    unset NO_PROXY no_proxy
}

auto_proxy() {
    if _port_listening; then
        enable_proxy
    else
        disable_proxy
    fi
}

proxy_status() {
    echo "Proxy port: ${PROXY_HOST}:${PROXY_PORT}"

    if _port_listening; then
        echo "Port status: LISTENING"
    else
        echo "Port status: NOT LISTENING"
    fi

    echo
    echo "HTTP_PROXY=${HTTP_PROXY:-<unset>}"
    echo "HTTPS_PROXY=${HTTPS_PROXY:-<unset>}"
    echo "ALL_PROXY=${ALL_PROXY:-<unset>}"
    echo "NO_PROXY=${NO_PROXY:-<unset>}"
}

# Run once when shell starts
auto_proxy

# Optional aliases
alias proxy-on='enable_proxy'
alias proxy-off='disable_proxy'
alias proxy-check='proxy_status'
alias proxy-tunnel='ssh -v -N -L 127.0.0.1:20122:127.0.0.1:43789 A6000'

# ===== Auto proxy config for local forwarded proxy ===== END
```

### VS Code

打开本地 VS Code `settings.json`：
`Ctrl + Shift + P`   `Preferences: Open User Settings (JSON)`
添加：
```json
{
  "remote.SSH.remoteEnvironment": {
    "ALL_PROXY": "socks5h://127.0.0.1:20122",
    "all_proxy": "socks5h://127.0.0.1:20122",
    "NO_PROXY": "localhost,127.0.0.1,::1",
    "no_proxy": "localhost,127.0.0.1,::1"
  }
}
```

重启远端 VS Code Server
`Ctrl + Shift + P`  `Remote-SSH: Kill VS Code Server on Host`
重新连接。


## 测试代理链路

登录服务器后执行：`ss -tulnp | grep 20122`
如果看到：`127.0.0.1:20122`
说明反向隧道成功。




# 配置 Codex 

