# 03-Agents

AI Agent 工具的安装、配置和项目级规则入口。

<table>
  <thead>
    <tr>
      <th>主题</th>
      <th>链接</th>
      <th>说明</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>AI Agent 使用</td>
      <td><a href="./README.md">README.md</a></td>
      <td>查看英文版 Agent 安装与配置说明。</td>
    </tr>
    <tr>
      <td>Karpathy Skills</td>
      <td><a href="./andrej-karpathy-skills/README.zh.md">andrej-karpathy-skills/README.zh.md</a></td>
      <td>参考已有中文说明配置用户级 Agent 规则。</td>
    </tr>
  </tbody>
</table>

## 安装 Agents

<table>
  <thead>
    <tr>
      <th>Agent / 工具</th>
      <th>安装命令</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>CC-Switch-CLI</strong></td>
      <td><code>curl -fsSL https://github.com/SaladDay/cc-switch-cli/releases/latest/download/install.sh | bash</code></td>
    </tr>
    <tr>
      <td><strong>Codex</strong></td>
      <td><code>npm i -g @openai/codex</code></td>
    </tr>
    <tr>
      <td><strong>Opencode</strong></td>
      <td><code>curl -fsSL https://opencode.ai/install | bash</code></td>
    </tr>
    <tr>
      <td><strong>Claude Code 与 CC-Switch</strong></td>
      <td><code>curl -fsSL https://claude.ai/install.sh | bash</code></td>
    </tr>
  </tbody>
</table>

## 配置 Agents

### Skills

- [Andrej Karpathy Skills](https://github.com/forrestchang/andrej-karpathy-skills/tree/main)

### `AGENTS.md`

- **用户级规则**
  - 可以参考 [Andrej Karpathy Skills](https://github.com/forrestchang/andrej-karpathy-skills/tree/main) 作为用户级指令。
  - 可通过输入 `Summarise all rules and instructions level by level` 测试规则加载情况。
  - 通常使用 `AGENTS.md` 保存规则。
- **项目级规则**
  - 可参考 [Agent Style](https://github.com/yzhao062/agent-style)。
  - 安装命令：`uv tool install agent-style`
  - 在项目或论文目录中执行：`agent-style enable agents-md`
