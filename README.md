# Wiki Plugin

在任意项目维护一个 `.wiki/` 知识库，让 LLM 直接读 wiki 回答项目问题，不用每次重读全量代码。

## 安装

### 方式一：手动配置（稳定，推荐）

由于 `/plugin install` 目前存在 bug（报成功但未写入 installed_plugins.json），推荐手动完成以下 4 步：

**1. 克隆仓库**
```bash
# 先清理旧目录（如果有）
rm -rf ~/.claude/plugins/wiki
rm -rf ~/.claude/plugins/cache/wiki-marketplace

git clone git@github.com:ye-c/wiki-sync.git ~/.claude/plugins/wiki
```

**2. 添加 marketplace 到 known_marketplaces.json**

在 `~/.claude/plugins/known_marketplaces.json` 中添加 entry（文件不存在会自动创建）。注意路径用实际路径：
```json
{
  "yec-plugins": {
    "source": {
      "source": "file",
      "path": "/Users/<your-username>/.claude/plugins/wiki/.claude-plugin/marketplace.json"
    },
    "installLocation": "/Users/<your-username>/.claude/plugins/wiki",
    "lastUpdated": "2026-05-09T00:00:00.000Z"
  }
}
```

**3. 添加插件到 installed_plugins.json**

在 `~/.claude/plugins/installed_plugins.json` 的 `plugins` 对象中添加 entry。注意：该文件有外层 `version` 和 `plugins` 包装，**不要**替换整个文件，只在 `plugins` 对象内插入：
```json
{
  "version": 2,
  "plugins": {
    "wiki@yec-plugins": [
      {
        "scope": "user",
        "installPath": "/Users/<your-username>/.claude/plugins/wiki",
        "version": "0.1.0",
        "installedAt": "2026-05-09T00:00:00.000Z",
        "lastUpdated": "2026-05-09T00:00:00.000Z"
      }
    ]
  }
}
```

**4. 在 settings.json 的 enabledPlugins 中启用**
```json
"wiki@yec-plugins": true
```

完成后重启 Claude Code。

### 方式二：尝试 /plugin 命令（可能有 bug）

```bash
# 添加 marketplace
/plugin marketplace add ye-c/wiki-sync

# 安装插件
/plugin install wiki@yec-plugins
```

如果 `/plugin install` 后 `/wiki:init` 报 unknown skill，需要手动修复 `installed_plugins.json`（参照方式一第 3 步）。

### 方式三：本地开发调试

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
1. /wiki:init        # 初始化 + 全量生成 wiki
2. /wiki:sync        # 每次代码变更后增量更新
3. /wiki:query       # 日常提问，基于 wiki 回答
```

## 要求

- Claude Code 1.0+
- 项目根目录有 `.git/`（wiki:sync 和 wiki:update 依赖 git）

## License

MIT
