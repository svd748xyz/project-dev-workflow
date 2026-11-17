# project-dev-workflow

一套给 AI 编码代理用的开发纪律 skill：先复述需求再动手、分层计划逐层验收、关键决策主动挑刺、踩坑实时记录。按任务大小分轻量 / 标准两档，避免为了流程而流程。

适用于任何加载 `SKILL.md` 的代理：Claude Code、Codex CLI、Cursor、OpenCode、Copilot agent mode 等。

[English README](README.md) · MIT 许可 · 无脚本、无网络调用，纯指令

## 内容

| 文件 | 作用 |
|---|---|
| `SKILL.md` | 主文件：入口（单项调用路由、轻量 / 标准档位、自主执行模式）+ 流程 0–7（开工、确认、分层计划、验收、汇报、挑刺、主动推进、踩坑记录） |
| `references/subagent-prompts.md` | 子代理提示词模板：验收子代理、批评者子代理、多视角审查、多方意见汇总 |
| `references/pitfalls-template.md` | 踩坑文档模板：项目级 `PITFALLS.md` 与本机级 `PITFALLS.local.md` 的骨架、条目格式和示例 |
| `LICENSE` | MIT |

## 安装

克隆到代理的 skills 目录，使 `SKILL.md` 位于某个 skill 目录根部：

```bash
git clone https://github.com/svd748xyz/project-dev-workflow ~/.claude/skills/project-dev-workflow   # Claude Code，个人级
git clone https://github.com/svd748xyz/project-dev-workflow .claude/skills/project-dev-workflow     # Claude Code，项目级
```

Windows（PowerShell）：

```powershell
git clone https://github.com/svd748xyz/project-dev-workflow "$env:USERPROFILE\.claude\skills\project-dev-workflow"
```

其他兼容 SKILL.md 的代理读各自的 skills 目录；共用同一目录的套装通常为 `~/.agents/skills/`（Windows 上 `%USERPROFILE%\.agents\skills\`）。代理完全不支持 skill 时，把 `SKILL.md` 正文粘进它读取的规则文件（`AGENTS.md` / `CLAUDE.md` 等）。

目录结构：

```
<skills-dir>/project-dev-workflow/
├── SKILL.md
├── LICENSE
├── README.md
└── references/
    ├── pitfalls-template.md
    └── subagent-prompts.md
```

无需安装依赖、无需配置。子代理能力可选但推荐：验收、挑刺、多视角审查都靠它；环境不支持时 skill 会降级为自己切角色核验。

### 踩坑文档

坑分两种范围：

- **项目级**（换台机器也会遇到）：写入项目根目录 `PITFALLS.md`，随仓库提交。
- **本机级**（只和当前机器有关）：写入 `~/.agents/PITFALLS.local.md`，不进仓库。

文件还不存在时，从 `references/pitfalls-template.md` 复制对应模板。之后每解决一个坑立即追加；条目过时直接改原条目；条目多了在顶部维护索引。

为了让没加载本 skill 的代理也能看到项目踩坑，建议在项目的 `AGENTS.md` / `CLAUDE.md` 中加一行"开工前先读根目录 PITFALLS.md"（skill 会在首次创建时提议，经你同意后才改）。

## 调用方式

- **完整流程**："按流程来"、"规范开发"，或多步骤、需求有歧义的开发任务自动触发。
- **单项调用**：只执行对应环节——"挑挑刺 / 评审方案"（§5）、"验收一下"（§3）、"记一下踩坑"（§7）。
- **自主执行**：说"直接做 / 不用问我"时，不等确认，按推荐项自行决定并在汇报中列出假设；不可逆操作仍会留给你决定。

## 更新记录

- **1.1.0**：新增轻量 / 标准档位、单项调用路由、自主执行模式；收紧确认触发条件；明确验收子代理权限与分歧处理；新增工作区检查与提交检查点；踩坑区分项目级 / 本机级并控制体积；精简汇报模板；批评者增加置信度与条数上限。
- **1.0.0**：初版。

## License

[MIT](LICENSE)。可自由使用、修改、再分发，商用亦可，保留版权声明即可。
