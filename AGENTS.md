# Sever-Setup — 新服务器开荒指南

新服务器（Ubuntu/Debian）环境搭建的文档与脚本集合。覆盖终端配置、编码环境、网络代理、科研项目模板及 AI Agent 工具安装。

## Architecture

```
01-WorkSpace/              # 服务器工作环境
├── terminal/              # Zsh + Oh-My-Zsh 安装、Tmux 配置
│   ├── zsh/install.sh     # Oh-My-Zsh 官方安装脚本（578 行）
│   ├── TMUX.md / ZSH.md   # 中文配置说明
├── code/                  # Python 环境 (uv/conda)、GitHub 认证
├── network/               # Clash + SSH 反向代理，内网出站方案
│   └── PROXY.md           # ssh -R 隧道、环境变量、conda 代理
├── 00-setup.sh            # 占位脚本（待完善）
├── Codex 服务器配置 ...md  # Codex + 代理链路的完整教程

02-Research/               # 科研项目模板
├── Algorithm-Research/    # 算法研究模板 + AGENTS.md 编码规范
├── System-Research/       # 系统研究模板
├── Paper-Writing/         # LaTeX 论文写作模板 (.latexmkrc)
├── Presentation/          # LaTeX Beamer 演示模板
└── Poster/                # 海报模板

03-Agents/                 # AI Agent 工具
├── andrej-karpathy-skills/  # git submodule (Karpathy 用户级规则)
└── skills/                   # 本地 skills 目录
```

## Key Files

| 文件 | 作用 |
|------|------|
| `01-WorkSpace/terminal/zsh/install.sh` | Oh-My-Zsh 安装器，支持离线/自定义路径 |
| `01-WorkSpace/network/PROXY.md` | SSH 反向代理 + Clash 内网穿透方案 |
| `01-WorkSpace/Codex 服务器配置 （无网 + 代理）.md` | Codex + 代理链路完整教程 |
| `02-Research/Algorithm-Research/AGENTS.md` | 研究项目的 Python 编码规范（uv、导入、模块组织） |
| `.gitmodules` | 定义 tmux.conf 和 andrej-karpathy-skills 两个子模块 |
| `.gitignore` | 忽略 source/、`.codewhale/*`、`.deepseek/` |

## Documentation Conventions

### README 层级规则
- **中文优先**：每个目录如有 `README_zh.md`，中文版为权威来源；`README.md` 需基于中文版更新。
- **语言切换**：`README.md` 和 `README_zh.md` 必须互链（`English \| 中文`）。
- **子目录 AGENTS.md**：每个目录的 `README.md` 亦作为该目录的 `AGENTS.md`。

### README 文件格式
每个目录的 README 应遵循此结构：

```markdown
# 目录标题

简短介绍段落。

<!-- HTML 概览表：链向子主题的超链接 -->
<table>
  <tr><td>Topic</td><td><a href="./subdir">subdir</a></td></tr>
</table>

## 子主题标题

详细说明。如内容过少，保留简单引导说明。
```

### 科研项目 README 模板
科研目录（如 Algorithm-Research、System-Research）包含标准子目录：
- `src/` — 核心库
- `evaluation/` — 正式实验/基准测试
- `motivation/` — 探索性实验
- 配置：`pyproject.toml` + `uv` 管理依赖

## Research Project Conventions

参见 `02-Research/Algorithm-Research/AGENTS.md`，核心规则：

- **Python 环境**：使用 `.venv` + `uv`（`pyproject.toml` 定义依赖）
- **运行脚本**：从项目根目录执行 `uv run path/to/file.py` 或 `uv run -m package.module`
- **禁止 sys.path**：`src/`、`test/`、`evaluation/`、`motivation/` 下的代码不得注入 `sys.path`
- **GPU 脚本**：需要 GPU 的脚本不自动运行，留给用户执行
- **模块组织**：
  - `src/` — 共享核心库，干净、模块化
  - `evaluation/` — 包含 `baseline/`、`benchmark/`、入口脚本 `eval_*.py`、结果目录 `results-*/`
  - `motivation/` — 按 `moti_<主题>/` 分组的小实验
  - `test/` — 对 `src/` 的正确性测试

### LaTeX 项目结构
Paper-Writing 和 Presentation 共用：
- `main.tex` + `main.bib` 作为入口
- `sty/` — 样式文件
- `figures/`、`plots/` — 图表
- `src/` — 章节/幻灯片文件
- `.latexmkrc` + `*.code-workspace` — 编译配置
- `.out/` — 编译输出目录

## Git Workflow

- **仓库**：`https://github.com/Tonanguyxiro/Sever-Setup.git`
- **主分支**：`main`
- **提交风格**：英文简洁描述（如 "Add README and AGENTS files for various research projects"）
- **子模块**：`git submodule update --init --recursive` 初始化

## Tips for AI Agents

- **这是文档项目，不是代码项目** — 没有构建系统、测试或 CI。不要尝试 `cargo build`、`npm test` 等。
- **中文是主要语言** — 多数文档为中文撰写或中文优先。生成新内容时用中文编写 README_zh.md，然后更新英文版。
- **代理配置是核心痛点** — 许多文档围绕内网服务器出站方案展开（SSH -R、Clash、环境变量）。修改代理相关内容需保持步骤可复现。
- **README 即 AGENTS.md** — 编辑完子目录的 README 后，它同时也是该目录下 AI Agent 会读取的 AGENTS.md。保持内容准确、可操作。
- **`00-setup.sh` 是占位符** — 目前只有 `ls` 命令。如需扩展，写入可重入、幂等的 shell 命令。
- **子目录 AGENTS.md 分层** — 根 AGENTS.md（本文件）描述项目级约定；`02-Research/*/AGENTS.md` 描述研究模块级约定。两者互不覆盖。
- **双语文档同步** — 更新中文版后，确保英文版同步关键信息（至少包含相同的 HTML 导航表和链接）。
