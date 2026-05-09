# Wiki Plugin

在任意项目维护一个 `.wiki/` 知识库，让 LLM 直接读 wiki 回答项目问题，不用每次重读全量代码。

## 安装

### 方式一：从 GitHub 安装（推荐）

```bash
# 添加 marketplace
/plugin marketplace add yec/wiki-sync

# 安装插件
/plugin install wiki
```

### 方式二：本地开发调试

```bash
cc --plugin-dir ~/code/wiki-sync
```

## 命令

| 命令 | 作用 |
|------|------|
| `/wiki:init` | 在当前项目创建 `.wiki/` 骨架 |
| `/wiki:sync` | 全量解析项目 → 生成完整 wiki |
| `/wiki:update` | 增量更新：git diff 变更 → 更新对应 wiki 页 |
| `/wiki:query <问题>` | 基于 wiki 回答项目问题 |

## 设计原则

- 最小化
- 人可编辑
- git 友好
- 不用向量库

## 目录结构

```
project/
  .wiki/
    config.json       # 包含/排除规则
    schema/
      system.md       # LLM 代码解析规则 + wiki 写作规范
    wiki/
      index.md        # 项目总览
      modules/        # 模块页
      apis/           # API 页
      models/         # 数据模型页
      flows/          # 业务流程页
```

## 工作流程

```
1. /wiki:init        # 初始化 wiki 骨架
2. /wiki:sync        # 全量生成 wiki
3. /wiki:update      # 每次代码变更后增量更新
4. /wiki:query       # 日常提问，基于 wiki 回答
```

## 要求

- Claude Code 1.0+
- 项目根目录有 `.git/`（wiki:sync 和 wiki:update 依赖 git）

## License

MIT
