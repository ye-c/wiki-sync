# Wiki Plugin

在任意项目维护一个 `.wiki/` 知识库，让 LLM 直接读 wiki 回答项目问题，不用每次重读全量代码。

## 安装

### 方式一：手动配置（稳定，推荐）

由于 `/plugin install` 目前存在 bug（报成功但未写入 installed_plugins.json），推荐手动完成以下 5 步：

**1. 克隆仓库到 marketplaces 目录**
```bash
git clone git@github.com:ye-c/wiki-sync.git ~/.claude/plugins/marketplaces/wiki-marketplace
```

**2. 创建符号链接**
```bash
ln -s ~/.claude/plugins/marketplaces/wiki-marketplace ~/.claude/plugins/wiki
```

**3. 添加 marketplace 到 known_marketplaces.json**
```json
"wiki-marketplace": {
  "source": {
    "source": "file",
    "path": "/Users/yec/.claude/plugins/marketplaces/wiki-marketplace/.claude-plugin/marketplace.json"
  },
  "installLocation": "/Users/yec/.claude/plugins/marketplaces/wiki-marketplace",
  "lastUpdated": "2026-05-09T00:00:00.000Z"
}
```

**4. 添加插件到 installed_plugins.json**
```json
"wiki@wiki-marketplace": [
  {
    "scope": "user",
    "installPath": "/Users/yec/.claude/plugins/wiki",
    "version": "0.1.0",
    "installedAt": "2026-05-09T00:00:00.000Z",
    "lastUpdated": "2026-05-09T00:00:00.000Z"
  }
]
```

**5. 在 settings.json 的 enabledPlugins 中启用**
```json
"wiki@wiki-marketplace": true
```

完成后重启 Claude Code。

### 方式二：尝试 /plugin 命令（可能有 bug）

```bash
# 添加 marketplace
/plugin marketplace add ye-c/wiki-sync

# 安装插件
/plugin install wiki@yec-plugins
```

如果 `/plugin install` 后 `/wiki:init` 报 unknown skill，需要手动修复 `installed_plugins.json`（参照方式一第 4 步）。

### 方式三：本地开发调试

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
