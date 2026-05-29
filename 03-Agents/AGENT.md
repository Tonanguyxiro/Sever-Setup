# 编程 Agents 配置

## 安装 Agents

```shell
curl -fsSL https://github.com/SaladDay/cc-switch-cli/releases/latest/download/install.sh | bash
```

### Codex 
```shell
npm i -g @openai/codex
```

### Opencode
```shell
curl -fsSL https://opencode.ai/install | bash
```

### Claude Code & CC-Switch

```shell
curl -fsSL https://claude.ai/install.sh | bash
```


## 配置 Agents

### 规则路径


### 基础 Skills

#### 安装 [Andrej Karpathy Skills](https://github.com/forrestchang/andrej-karpathy-skills/tree/main)

```shell
cd ~
git clone https://github.com/forrestchang/andrej-karpathy-skills.git
cp andrej-karpathy-skills/CLAUDE.md andrej-karpathy-skills/AGENTS.md
```

```shell
if [ -d "$HOME/.agents" ]; then
    echo "Directory ~/.agents already exists. Copying AGENTS.md to ~/.agents/AGENTS.md"
    cp andrej-karpathy-skills/AGENTS.md "$HOME/.agents/AGENTS.md"
else
    echo "Directory ~/.agents does not exist. Creating directory and copying AGENTS.md to ~/.agents/AGENTS.md"
    mkdir -p "$HOME/.agents"
    cp andrej-karpathy-skills/AGENTS.md "$HOME/.agents/AGENTS.md"
fi
```

```shell
cp .agents/AGENTS.md .codex
```

### 论文 Skills

#### 安装 [Agent Style](https://github.com/yzhao062/agent-style)

```shell
uv tool install agent-style
```

```shell
agent-style enable agents-md
```