# 03-Agents

<table>
  <thead>
    <tr>
      <th>Topic</th>
      <th>Link</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>AI Agent Usage</td>
      <td><a href="./AGENT.md">AGENT.md</a></td>
    </tr>
  </tbody>
</table>

## Install Agents

<table>
  <thead>
    <tr>
      <th>Agent / Tools</th>
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
      <td><strong>Claude Code &amp; CC-Switch</strong></td>
      <td><code>curl -fsSL https://claude.ai/install.sh | bash</code></td>
    </tr>
  </tbody>
</table>

## Configure Agents

### Skills 

- [](https://github.com/forrestchang/andrej-karpathy-skills/tree/main)

### `AGENTS.md`

- **User Level** Rules
    - [Andrej Karpathy Skills](https://github.com/forrestchang/andrej-karpathy-skills/tree/main)
        - Use as user level instruction
        - Test by input: `Summarise all rules and instructions level by level`
        - Normally we use `AGENTS.md`
- **Project Level** Rules
    - [Agent Style](https://github.com/yzhao062/agent-style): 
        - `uv tool install agent-style`​
        - In project/paper dictionary: `agent-style enable agents-md`