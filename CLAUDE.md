# CLAUDE.md

本文件为 Claude Code 提供 wiki-sync 插件的维护指导。

## 项目结构

```
wiki-sync/
├── .claude-plugin/
│   └── plugin.json          # 插件清单
├── skills/
│   ├── init/SKILL.md        # /wiki:init
│   ├── query/SKILL.md       # /wiki:query
│   ├── sync/SKILL.md        # /wiki:sync
│   └── update/SKILL.md      # /wiki:update
├── README.md
├── LICENSE
└── CLAUDE.md                # 本文件
```

## 架构决策

### 为什么用 skills 而不是 commands？

Wiki 插件的操作是**主动查询**（用户问问题）和**增量/全量生成**，不是简单的「执行命令然后结束」。Skills 支持更复杂的流程（多步推理、状态管理），更适合 wiki 的使用场景。

### 为什么不用向量库/Embedding？

- Wiki 页是人可读可编辑的 Markdown
- 轻量化，无需额外服务
- Git 友好，可以 code review wiki 变更

## 发布流程

1. 修改版本号在 `plugin.json`
2. Push 到 GitHub
3. 在目标机器上：`/plugin marketplace add ye-c/wiki-sync && /plugin install wiki`

## 维护注意

- SKILL.md 的 `name` 字段必须是 `wiki:<action>` 格式
- `plugin.json` 的 `name` 是插件名称（不带 wiki: 前缀）
- 测试用 `cc --plugin-dir ~/code/wiki-sync` 加载本地版本
