# Wiki Plugin

在任意项目维护一个 `.wiki/` 知识库，让 LLM 直接读 wiki 回答项目问题，不用每次重读全量代码。

## 安装

### 标准流程

```bash
/plugin marketplace add ye-c/wiki-sync
/plugin install wiki
/reload-plugins
```

### 本地开发调试

```bash
cc --plugin-dir ~/code/wiki-sync
```

## 命令

| 命令 | 作用 |
|------|------|
| `/wiki:init` | 全量生成完整 wiki |
| `/wiki:sync` | 增量更新：git diff 变更 → 更新对应 wiki 页 |
| `/wiki:query <问题>` | 基于 wiki 回答项目问题 |

## 设计原则

- 最小化
- 人可编辑
- git 友好
- 不用向量库

## 工作流程

```
1. /wiki:init        # 初始化 + 全量生成 wiki
2. /wiki:sync        # 每次代码变更后增量更新
3. /wiki:query       # 日常提问，基于 wiki 回答
```

## 多语言支持

默认配置仅适用于 **Python 项目**（`include: ["src/**/*.{py}"]`）。

### 修改为其他语言

编辑 `.wiki/config.json` 中的 `include` 字段：

```json
// TypeScript/Node.js
"include": ["src/**/*.{ts,js}"]

// Go
"include": ["**/*.go"]

// 混合项目
"include": ["src/**/*.{py}", "src/**/*.ts"]
```

相应调整 `exclude` 字段（如 Python 不需要 `node_modules`，但 Node.js 不需要 `.venv`）。

## 要求

- Claude Code 1.0+
- 项目根目录有 `.git/`（wiki:sync 和 wiki:query 依赖 git）

## License

MIT
