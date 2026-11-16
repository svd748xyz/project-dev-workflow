# project-dev-workflow

一套给 AI 编码代理用的开发纪律 skill：先复述需求再动手、分层计划逐层验收、关键决策主动挑刺、踩坑实时记录。

## 内容

| 文件 | 作用 |
|---|---|
| `SKILL.md` | 主文件：流程 0–7（开工、确认、分层计划、验收、汇报、挑刺、主动推进、踩坑记录） |
| `references/subagent-prompts.md` | 子代理提示词模板：验收子代理、批评者子代理、多视角审查、多方意见汇总 |
| `references/pitfalls-template.md` | `PITFALLS.md` 模板：项目首次建踩坑文档时复制其中的骨架与条目格式 |

## 用法

安装到代理的 skills 目录，使 `SKILL.md` 位于某个 skill 目录根部，例如：

```
<skills-dir>/project-dev-workflow/
├── SKILL.md
└── references/
    ├── pitfalls-template.md
    └── subagent-prompts.md
```

在 Windows 上常见路径为 `%USERPROFILE%\.agents\skills\project-dev-workflow\`。

### 踩坑文档

项目根目录还没有 `PITFALLS.md` 时，把 `references/pitfalls-template.md` 中 `---` 之间的内容复制过去，再按其中的"条目格式"填第一条。之后每解决一个坑立即追加；条目过时直接改原条目。开工前先读一遍。

## 调用方式

支持显式点名（"按流程来"、"验收一下"、"挑挑刺"、"记一下踩坑"），也支持隐式触发：多步骤、需求有歧义的开发任务会自动套用该流程。

## License

未声明，默认保留所有权利。需要复用请先联系作者。
