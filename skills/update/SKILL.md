---
name: wiki:update
description: 增量更新 wiki：基于 git diff 变更文件更新对应 wiki 页（update 命令）
---

# Wiki Update

增量更新 wiki：读取 git diff 变更文件，只更新涉及的 wiki 页面。

## 前置条件

- `.wiki/` 目录已存在（如果不存在，提示先运行 `/wiki:init`）

## 执行步骤

1. **读取 `.wiki/config.json`** 获取 `last_sync_commit`
   - 必须先执行此步，将 `last_sync_commit` 的值记录下来
   - **不要跳过此步，不要用 HEAD 替代**

2. **获取变更文件列表**
   ```bash
   git diff --name-only <上一步获取的last_sync_commit>...HEAD
   ```
   - 将 `<last_sync_commit>` 替换为第 1 步获取的实际 commit hash
   - 如果没有变更，提示"没有检测到代码变更"

3. **按路径判断属于哪个 wiki 模块**
   - 读取当前 wiki 结构
   - 将变更文件映射到对应模块页
   - 如果变更文件在 exclude 列表中（如 node_modules, .venv），跳过

4. **读取变更文件的 diff 内容**
   ```bash
   git diff <上一步的last_sync_commit>...HEAD -- <文件路径>
   ```

5. **LLM 单次调用处理每个受影响的模块**
   - 输入：变更文件内容 + diff + 对应 wiki 页 + schema/system.md
   - 输出：更新后的 wiki Markdown

6. **写入文件**
   - 检查文件 mtime 是否在读取后有修改（有冲突 → warning + 提示人手动合并）
   - 写入更新后的内容

7. **更新 `wiki/index.md`** 如果有新模块被添加

8. **更新 `config.json` 中的 `last_sync_commit`** 为当前 HEAD commit

## 冲突处理

- 读取 wiki 文件时记录 mtime
- 写入前检查 mtime 是否变化
- 有冲突 → warning + 提示手动合并，不覆盖

## 提示

- 删除文件 → 在对应 wiki 页标记 `[过时]`
- 新增文件 → 补充到对应 wiki 页
- 修改文件 → 替换对应段落
